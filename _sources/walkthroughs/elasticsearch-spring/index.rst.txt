:orphan:

.. _walkthrough-elasticsearch-spring:

====================================
Walkthrough: Elasticsearch in Spring
====================================

We'll walk through the steps to integrate Elasticsearch with Spring, using the Launchcart application.

The goal is to enable fuzzy searching for items via LaunchCart's REST API. This will require:

#. Configuring the application to work with Elasticsearch.
#. Creating a Java representation of the item documents we want to store in Elasticsearch.
#. Creating a REST endpoint that conducts a fuzzy search for item documents.

Getting Ready
=============

Spring Elasticsearch Configuration
----------------------------------

Checkout the :ref:`spring-elasticsearch` article to configure your project for Elasticsearch.

Write A Test
------------

Let's write an integration test for the desired behavior. Again, the goal of this tutorial is to enable fuzzy searching for items via LaunchCart's REST API.

We will soon create the classes ``ItemDocument`` and ``ItemDocumentController`` to implement fuzzy search, so let's name our test class ``ItemDocumentControllerTests``.

.. code-block:: java

    /*
     * In src/test/java/org/launchcode/launchcart/ItemDocumentControllerTests.java
     /*
    @RunWith(SpringRunner.class)
    @IntegrationTestConfig
    public class ItemDocumentControllerTests extends AbstractBaseRestIntegrationTest {

        @Autowired
        private MockMvc mockMvc;

        @Test
        public void testFuzzySearch() throws Exception {
            Item item = new Item("Test Item Again", 42);
            String json = json(item);
            mockMvc.perform(post("/api/items/")
                    .content(json)
                    .contentType(contentType));
            mockMvc.perform(get("/api/items/search?q={term}", "agan"))
                    .andDo(print())
                    .andExpect(status().isOk())
                    .andExpect(content().contentType(contentType))
                    .andExpect(jsonPath("$.length()").value(1))
                    .andExpect(jsonPath("$[0].name").value(item.getName()));
        }

    }

Run this test to make sure that it fails. The failure should look like this: ::

    java.lang.AssertionError: Status
    Expected :200
    Actual   :400

If your code throws an exception, review the steps above to make sure you have properly configured Spring Boot to talk to ES.

Model and Repository
--------------------

We need to create a new model class to represent the documents that we'll be storing in ES, along with a corresponding repository.

Create a new package, ``org.launchcode.launchcart.models.es``, and add the following class:

.. code-block:: java

    /*
     * /src/main/java/org/launchcode/launchcart/models/es/ItemDocument.java
     */
    @Document(indexName = "#{esConfig.indexName}", type = "items")
    public class ItemDocument {

        @Id
        @GeneratedValue(strategy= GenerationType.AUTO)
        private String id;

        private Integer itemUid;
        private String name;
        private double price;
        private boolean newItem;
        private String description;

        public ItemDocument() {}

        public ItemDocument(Item item) {
            this.itemUid = item.getUid();
            this.name = item.getName();
            this.price = item.getPrice();
            this.newItem = item.isNewItem();
            this.description = item.getDescription();
        }

        // Getters and setters omitted

    }



.. note:: The ``@Id`` annotation should come from the ``javax.persistence`` package, so be sure to select the correct import.

Review the fields and constructors for this class to make sure you understand what it represents. Each ``ItemDocument`` object will be a "copy" of an ``Item`` that is suitable for storing in Elasticsearch, and which keeps track of the original item's ID in the ``itemUid`` field.

There are two things to note about the ``ItemDocument`` class that make it different from our other persistent model classes.

1. The ID field for the class is of type ``String`` instead of ``Integer``. We do this because Elasticsearch uses hash strings as IDs instead of integers.
2. The ``@Document`` annotation notifies Spring that this class may be stored in Elasticsearch, using the index and type names provided. Notice the index name, ``#{esConfig.indexName}``. This uses Spring's expression language to dynamically insert the value of the ``indexName`` property of the ``EsConfig`` bean that we created earlier. Recall that this property is set using the value of ``es.index-name`` in the properties file, so it will be different for development and test contexts.

Also add a new repository, which extends ``ElasticsearchRepository``:

.. code-block:: java

    /*
     * src/main/java/org/launchcode/launchcart/data/ItemDocumentRepository.java
     */
    public interface ItemDocumentRepository 
        extends ElasticsearchRepository<ItemDocument, String> {

        Iterable<ItemDocument> search(QueryBuilder queryBuilder);

    }

Controller
----------

Create ``ItemDocumentController`` and implement the ``search`` method/endpoint.

.. code-block:: java

    /*
     * src/main/java/org/launchcode/launchcart/controllers/es/ItemDocumentController.java
     */
    @RestController
    @RequestMapping(value = "/api/items")
    public class ItemDocumentController {

        @Autowired
        private ItemDocumentRepository itemDocumentRepository;

        @GetMapping(value = "search")
        public List<ItemDocument> search(@RequestParam String q) {
            FuzzyQueryBuilder fuzzyQueryBuilder = QueryBuilders.fuzzyQuery("name", q);
            List<ItemDocument> results = new ArrayList<>();
            Iterator<ItemDocument> iterator = itemDocumentRepository.search(fuzzyQueryBuilder).iterator();

            while(iterator.hasNext()) {
                results.add(iterator.next());
            }

            return results;
        }

    }

Spring is unable to serialize (i.e. turn into XML or JSON) an ``Iterable`` object, so we must copy each of the results into a new ``List``. If we expect large results sets, we should use a paginated approach that only returns segments of the result set.

Elasticsearch Controller
------------------------

Create ``EsController`` and ``EsUtils`` to enable admin-oriented interactions with the ES instance.

.. code-block:: java

    /*
     * src/main/java/org/launchcode/launchcart/controllers/es/EsController.java
     */
    @RestController
    @RequestMapping(value = "/api/es")
    public class EsController {

        @Autowired
        private EsUtil esUtil;

        @PostMapping(value = "/refresh")
        public ResponseEntity refresh() {
            esUtil.refresh();
            return new ResponseEntity("Refreshed Elasticsearch index\n", HttpStatus.OK);

        }

    }

.. code-block:: java

    /*
     * src/main/java/org/launchcode/launchcart/util/EsUtil.java
     */
    @Component
    public class EsUtil {

        @Autowired
        private ItemRepository itemRepository;

        @Autowired
        private ItemDocumentRepository itemDocumentRepository;

        public void refresh() {
            itemDocumentRepository.deleteAll();
            List<ItemDocument> itemDocuments = new ArrayList<>();
            for(Item item : itemRepository.findAll()) {
                itemDocuments.add(new ItemDocument(item));
            }
            itemDocumentRepository.saveAll(itemDocuments);
        }
    }

Saving ItemDocuments
====================

While we have code in place to carry out searches in Elasticsearch via our API, there are not any documents in the ES index quite yet.

Within ``ItemController`` and ``ItemRestController``, let's save a new ``ItemDocument`` every time we create a new ``Item``.

We previously saved and returned a new ``Item`` like this:

.. code-block:: java

    itemRepository.save(item);

Now, however, we must also save an ``ItemDocument`` for each newly-created item:

.. code-block:: java

    Item savedItem = itemRepository.save(item);
    itemDocumentRepository.save(new ItemDocument(savedItem));

You will need to ``@Autowire`` a ``ItemDocumentRepository`` in each controller that needs access to Elasticsearch.

.. note:: We should also update or delete an ``ItemDocument`` whenever the corresponding ``Item`` is updated or deleted. We leave this exercise to you.

Testing
=======

Run all tests for your application. Hopefully, everything will pass. If not, review the test report and correct any issues.

Even if all of your tests all pass the first time, the new ``ItemDocumentControllerTests.testFuzzySearch`` test will fail the second time it is run. This is because the number of search results will be incorrect, since we failed to clear our Elasticsearch index after the first run. Unlike our in-memory relational test database, Elasticsearch will keep data from one test run to another.

We want to ensure that any documents created during testing are removed after each individual test has run.

Create a new base class, ``AbstractBaseIntegrationTest``:

.. code-block:: java

    public class AbstractBaseIntegrationTest {

        @Autowired
        private ItemDocumentRepository itemDocumentRepository;

        @After
        public void clearItemDocumentRepository() {
            itemDocumentRepository.deleteAll();
        }
    }

Then modify both ``AbstractBaseRestIntegrationTest`` and ``AbstractBaseCustomerIntegrationTest`` to extend this new base class. This will ensure that Elasticsearch data created by each test is deleted when the test has completed.

Refresh the Index
==================

While your tests are now passing, if you were to start up your application and try to conduct a fuzzy search (e.g. ``curl localhost:8080/api/items/search?q=shoe``) you would not receive any hits. If the reason why isn't obvious, it should become so after looking at the data in your ``launchcart`` index in Elasticsearch.

Since each of the items in our Postgres database was created *before* we added the Elasticsearch integration, the associate ``ItemDocument`` objects were not created. We can retroactively create the objects and documents using our special endpoint for refreshing the index. Start up your application, and make a request to this endpoint:

::

    $ curl -XPOST localhost:8080/api/es/refresh/

You should now see the exact number of documents in the ``launchcart`` Elasticsearch index as there are rows in the ``items`` table in Postgres.

Your Tasks
==========

On your own, study the code above and make sure you understand each of the components, referring to the linked resources below as necessary. When you come across something that isn't clear, talk through it with another student or with an instrutor.

Bonus Missions
==============

We looked at how to push a new item to Elasticsearch when creating it via the REST API. There are still several tasks that can be immediately carried out to fully integrate ES with the application. Try one more more of the following:

* We are currently creating and saving a new ``ItemDocument`` whenever a new ``Item`` is created, however, we are not updating or deleting an ``ItemDocument`` when the corresponding ``Item`` is updated or deleted. Add the code to do this.
* Add a search view that displays results of a fuzzy search. This may be done either by an AJAX request to ``ItemDocumentRepository.search``, or by creating a new controller method that passes fuzzy search results into a template.

Resources
=========

* `Spring Data Elasticsearch <http://www.baeldung.com/spring-data-elasticsearch-tutorial>`_
* `ElasticsearchRepository <https://docs.spring.io/spring-data/elasticsearch/docs/current/api/org/springframework/data/elasticsearch/repository/ElasticsearchRepository.html>`_
* `TransportClient <https://www.elastic.co/guide/en/elasticsearch/client/java-api/6.2/transport-client.html>`_
* `QueryBuilders <https://static.javadoc.io/org.elasticsearch/elasticsearch/2.4.0/org/elasticsearch/index/query/QueryBuilders.html>`_
* `Spring Data Elasticsearch Queries <http://www.baeldung.com/spring-data-elasticsearch-queries>`_
* `The @Value annotation <http://www.baeldung.com/spring-value-annotation>`_
