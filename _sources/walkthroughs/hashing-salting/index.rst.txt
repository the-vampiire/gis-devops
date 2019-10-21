:orphan:

.. _walkthrough-hashing-salting:

==========================================
Walkthrough: Hashing and Salting Passwords
==========================================

Setup
#####

1. Check out the ``rest-walkthrough-solution`` branch of `LaunchCodeTraining/launchcart <https://gitlab.com/LaunchCodeTraining/launchcart>`_
2. Create and checkout a **story branch** named ``securely-hash-passwords`` via ``$ git checkout -b securely-hash-passwords``

Determine the Status of the Current Code
########################################

1. Run tests to see current status
2. Search for any ``//TODO`` comments

Implement Secure Password Hashing using BCrypt
##############################################

Add the **Spring Security** dependency to ``build.gradle``::

    dependencies {
        compile('org.springframework.security:spring-security-crypto')
        //more dependencies will be listed above or below...
    }

Use the BCryptPasswordEncoder Class
#####################################

1. Create a ``static final`` instance of the encoder class, within ``User``:

   .. code-block:: java

        private static final BCryptPasswordEncoder encoder = new BCryptPasswordEncoder();

2. Use ``BCryptPasswordEncoder.encode`` in the ``User.java`` contructor:

   .. code-block:: java

        public User(String username, String password) {
            this.username = username;
            this.pwHash = hashPassword(password);
        }

        private static String hashPassword(String password) {
            return encoder.encode(password);
        }

3. Update ``isMatchingPassword(String password)``:

   .. code-block:: java

        public boolean isMatchingPassword(String password) {
            return encoder.matches(password, this.pwHash);
        }

Let's see if it works!
######################

1. Make sure the tests pass
2. Run ``bootRun`` and see if you can register (register a few users)
3. Verify what the hashed and salted password looks like in the database

Quiz
####

* Where is the salt stored?

Resources
#########

* `OWASP Hashing Guidelines <https://www.owasp.org/index.php/Password_Storage_Cheat_Sheet>`_
* `Spring Security - BCrypt documentation <https://docs.spring.io/spring-security/site/docs/5.0.7.RELEASE/reference/htmlsingle/#spring-security-crypto-passwordencoders>`_
* `Spring Security - BCrypt source code <https://github.com/spring-projects/spring-security/blob/5888a3f6b7e9f56e5d47ecbea3444209c713e3bf/crypto/src/main/java/org/springframework/security/crypto/bcrypt/BCryptPasswordEncoder.java>`_
* `In Depth Background and Guidance for Hashing <https://crackstation.net/hashing-security.htm>`_
* `Bcrypt <https://en.wikipedia.org/wiki/Bcrypt>`_
