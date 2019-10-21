:orphan:

.. _elasticsearch-spring-walkthrough-old:

====================================
Walkthrough: Elasticsearch in Spring
====================================

We'll walk through the steps to integrate Elasticsearch with Spring, using the Launchcart application.

The goal is to enable fuzzy searching for items via LaunchCart's REST API. This will require:

1. Configuring the application to work with Elasticsearch.
2. Creating a Java representation of the item documents we want to store in ES.
3. Creating a REST endpoint that conducts a fuzzy search for item documents.

Integrating Elasticsearch
=========================

We'll walk through several steps needed to use Elasticsearch within Spring.

.. note::

    The code that we'll look at is in the ``elasticsearch`` branch of the ``LaunchCodeTraining/launchcart`` repository.

    To view the specific changes, look at `this commit <https://gitlab.com/LaunchCodeTraining/launchcart/commit/7e610e2604997fdd54c6ea49a7f1a9b275a93a89>`_.


Add Gradle dependencies
-----------------------

We need two new dependencies in order to connect ES with Spring Boot.

::

    compile('org.springframework.boot:spring-boot-starter-data-elasticsearch:1.5.10.RELEASE')
    compile(group: 'org.elasticsearch.client', name: 'transport', version: '6.5.2')

.. warning::

    This approach uses the ``TransportClient`` class to connect to a cluster over port 9300 via the transport protocol. This technique requires that the ES instance and the ``TransportClient`` have the *same major versions*.

    Check your version of Elasticsearch by running ``curl localhost:9200``.


.. note::

    The Spring Data project is in the process of replacing this client with a REST API client that will be version agnostic.

    Read more about the state of the official `Elasticsearch Java clients <https://www.elastic.co/blog/state-of-the-official-elasticsearch-java-clients>`_.

Configuring Spring Boot for ES
------------------------------

Let's create the ``EsConfig`` class to setup an embedded Elasticsearch instance.

This class will handle connecting to the ES server.

.. code-block:: java

    /*
     * In /src/java/main/org/launchcode/launchcart/EsConfig.java
     */
    @Configuration
    public class EsConfig {

        // Pull the name of the index from .properties file
        @Value("${es.cluster-name")
        private String clusterName;

        @Bean
        public ElasticsearchOperations elasticsearchTemplate() throws UnknownHostException {

            Settings settings = Settings.builder()
                    .put("cluster.name", clusterName).build();

            TransportClient client = new PreBuiltTransportClient(settings);
            TransportAddress address = new InetSocketTransportAddress(InetAddress.getByName("localhost"), 9300);
            client.addTransportAddress(address);
            return new ElasticsearchTemplate(client);
        }
    }

Define the index name in ``application.properties``.

::

    es.cluster-name=elasticsearch


.. warning::

    The Elasticsearch instance that will be used in this integration **will not** be the same instance that you installed previously. This integration method creates and embedded ES cluster that only accepts connections via the Transport Client.

    This means that you can't use the REST API to interact with it directly.

Write A Test
------------

Following TDD, let's write an integration test for the desired behavior.

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


As with our Postgres database, our tests should operate on a different data store instance than we use during development. In ``application-test.properties`` set:

::

    es.cluster-name=elasticsearch_test

Finally, we want to ensure that any documents created during testing are removed each test has run.

In ``AbstractBaseRestIntegrationTest``, add this snippet:

.. code-block:: java

    @Autowired
    private ItemDocumentRepository itemDocumentRepository;

    @After
    public void clearItemDocumentRepository() {
        itemDocumentRepository.deleteAll();
    }


Model and Repository
--------------------

We need to create a new model class to represent the documents that we'll be storing in ES, along with a corresponding repository.

.. code-block:: java

    /*
     * /src/main/java/org/launchcode/launchcart/models/es/ItemDocument.java
     */
    @Document(indexName = "launchcart", type = "items")
    public class ItemDocument {

        @Id
        private Integer id;
        private String name;
        private double price;
        private boolean newItem;
        private String description;

        public ItemDocument() {}

        public ItemDocument(Item item) {
            this.id = item.getUid();
            this.name = item.getName();
            this.price = item.getPrice();
            this.newItem = item.isNewItem();
            this.description = item.getDescription();
        }

        public Integer getId() {
            return id;
        }

        public void setId(Integer id) {
            this.id = id;
        }

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }

        public double getPrice() {
            return price;
        }

        public void setPrice(double price) {
            this.price = price;
        }

        public boolean isNewItem() {
            return newItem;
        }

        public void setNewItem(boolean newItem) {
            this.newItem = newItem;
        }

        public String getDescription() {
            return description;
        }

        public void setDescription(String description) {
            this.description = description;
        }
    }


And the repository, which extends ``ElasticsearchRepository``:

.. code-block:: java

    /*
     * src/main/java/org/launchcode/launchcart/data/ItemDocumentRepository.java
     */
    public interface ItemDocumentRepository 
        extends ElasticsearchRepository<ItemDocument, Integer> {

        Iterable<ItemDocument> search(QueryBuilder queryBuilder);

    }

Controller
----------

Create ``ItemDocumentController`` and implement the ``search`` method

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
            fuzzyQueryBuilder.fuzziness(Fuzziness.AUTO);
            List<ItemDocument> results = new ArrayList<>();
            Iterator<ItemDocument> iterator = itemDocumentRepository.search(fuzzyQueryBuilder).iterator();

            while(iterator.hasNext()) {
                results.add(iterator.next());
            }

            return results;
        }

    }

ES Controller
-------------

Create ``EsController`` and ``EsUtils`` to enable admin-oriented interactions with the ES instance

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
        public ResponseEntity<String> refresh() {
            esUtil.refresh();
            return new ResponseEntity<>("Health", HttpStatus.OK);
        }

    }


    /*
     * src/main/java/org/launchcode/launchcart/util/EsUtil.java
     */
    @Component
    public class EsUtil {

        @Autowired
        private ItemRepository itemRepository;

        @Autowired
        private ItemDocumentRepository itemDocumentRepository;

        private static Logger logger = LoggerFactory.getLogger(EsUtil.class);

        public void refresh() {
            logger.info("Deleting all documents from ItemDocumentRepository");
            itemDocumentRepository.deleteAll();
            List<ItemDocument> itemDocuments = new ArrayList<>();
            for(Item item : itemRepository.findAll()) {
                itemDocuments.add(new ItemDocument(item));
            }
            itemDocumentRepository.save(itemDocuments);
        }
    }

Saving ItemDocuments
====================

While we have code in place to carry out searches in Elasticsearch via our API, there are not any documents in ES yet.

Within ``ItemController`` and ``ItemRestController``, let's save a new ``ItemDocument`` every time we create a new ``Item``.

.. code-block:: java

    itemDocumentRepository.save(new ItemDocument(item));

.. note:: We should also update or delete an ``ItemDocument`` whenever the corresponding ``Item`` is updated or deleted. We leave this exercises to you.

Refresh the Index
=================

Finally, we want to make sure that existing items are in Elasticsearch. To do this, we can use our special endpoint for reindexing. Start up your application, and make a request to this endpoint:

::

    $ curl -XPOST localhost:8080/api/es/refresh/

Your Tasks
==========

On your own, study the code above and make sure you understand each of the components, referring to the linked resources below as necessary. When you come across something that isn't clear, talk through it with another student or with an instrutor.

Bonus Missions
==============

We looked at how to push a new item to Elasticsearch when creating it via the REST API. There are still several tasks that can be immediately carried out to fully integrate ES with the application. Try one more more of the following:

* We are currently creating and saving a new `ItemDocument` whenever a new `Item` is created, however, we are not updating or deleting an `ItemDocument` when the corresponding `Item` is updated or deleted. Add the code to do this.
* Add a search view that displays results of a fuzzy search. This may be done either via an AJAX request to ``ItemDocumentRepository.search``, or by creating a new controller method that passes fuzzy search results into a template.

Resources
=========

* `Spring Data Elasticsearch <http://www.baeldung.com/spring-data-elasticsearch-tutorial>`_
* `ElasticsearchRepository <https://docs.spring.io/spring-data/elasticsearch/docs/current/api/org/springframework/data/elasticsearch/repository/ElasticsearchRepository.html>`_
* `TransportClient <https://www.elastic.co/guide/en/elasticsearch/client/java-api/6.2/transport-client.html>`_
* `QueryBuilders <https://static.javadoc.io/org.elasticsearch/elasticsearch/2.4.0/org/elasticsearch/index/query/QueryBuilders.html>`_
* `Spring Data Elasticsearch Queries <http://www.baeldung.com/spring-data-elasticsearch-queries>`_
* `The @Value annotation <http://www.baeldung.com/spring-value-annotation>`_
