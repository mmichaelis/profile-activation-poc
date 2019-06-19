# Profile Activation Tests

Most examples for profile activation are quite straightforward, not dealing with
more complex scenarios. This may be due to the fact, that Maven profile activation
is kept quite simple and there are not much sophisticated scenarios to describe with
out-of-the box profile activation.

## Scenarios

The proof-of-concept POMs you will find in here are for the following scenario: 

* You have a multi-module project building different applications.

* There are other modules containing integration and system tests which sometimes
    test only one application, sometimes they test two or more applications and
    how they interact with each other.

* The plugin to run the integration tests has a flag which tells if to run
    the tests or not (like `maven-surefire-plugin` and the property `skipTests`).

* Modifying application "client" you now want to run all tests, which provide
    sufficient confidence, that you did not break the client. You do so by telling
    your Maven build, that your application under test (AUT) is "client":
    
    ```text
    $ mvn verify -Daut=client
    ```

* Advanced scenarios:

    1. Now you did changes to "client" and "server". Assuming that
        there are even more applications (not covered by these examples) you want to
        run all tests in one Maven run, which are required. Suggested Maven call:

        ```text
        $ mvn verify -Daut=client,server
        ```

    2. Sometimes you don't want to specify any AUT but instead run all tests. For example,
        before merging a pull request. To do so, you just skip specifying any AUT:
        
        ```text
        $ mvn verify
        ```

## Examples

### Basic Maven Profiles

* **client-only-itest:**

    This is the most simple use case. These tests will run if the only AUT specified
    is "client".
    
* **client-or-server-exclusive-itest:**

    These tests will run if the AUT is either "client" or "server" but not both.

* **client-contained-multi-properties-itest:**

    This approach will switch test execution on property name rather than property value.
    Benefit: We can now cover the scenario to run tests, if the module applies to one
    of several specified AUTs without using an extension.
    
    Caveat: We either end up in huge POMs with many profiles if we have a bunch of
    AUTs to support (we need to explicitly list all AUTs not covered by this module), or
    we need an extra property to actually activate selective AUT execution.
    
    In the example we have chosen the second option, as the POMs will be much less
    verbose. The AUT selection will then look like as given in the examples below:
    
    ```text
    $ mvn verify -Duse-aut -Daut-client
    $ mvn verify -Duse-aut -Daut-server
    $ mvn verify -Duse-aut -Daut-client -Daut-server
    ``` 

    Where only in the second example the tests will not run.

### Profile Activation Extensions

As stated before, standard Maven profile activation is rather restricted. That is
why we may require to use extensions for profile activation (see [Custom Profile Activators][ASF-Wiki-ProfileActivators]).

#### random-maven/profile-activator-extension

The [Profile Activator Extension][profile-activator-extension] by random-maven seems
to be the only alive-and-kicking extension in 2019 which provides support for
script-based profile activation. Previous approaches seem to be dead since 2016.

* **client-contained-itest:**

    Based on this extension, these tests are able to run, if "client" is contained in
    the specified AUTs provided as CSV value. Thus, these tests will run for
    `aut=client` as well as for `aut=client,server`.

## See Also

* Apache Software Foundation: [Custom Profile Activators - Maven][ASF-Wiki-ProfileActivators]
* Apache Software Foundation: [\[MNG-3328\] Allow multiple profile activation properties.][MNG-3328]
* Apache Software Foundation: [\[MNG-6345\] Support profile activation via script.][MNG-6345]
* Baeldung: [Guide to Maven Profiles][baeldung-maven-profiles]
* GitHub: [random-maven/profile-activator-extension: Provide flexible Maven profile activation via script][profile-activator-extension]
* Stack Overflow: [Activation of maven profile based on multiple properties][stackoverflow-5417437]
* Wikipedia: [MVEL][wikipedia-en-mvel]

[ASF-Wiki-ProfileActivators]: <https://cwiki.apache.org/confluence/display/MAVENOLD/Custom+Profile+Activators> "Custom Profile Activators - Maven - Apache Software Foundation"
[baeldung-maven-profiles]: <https://www.baeldung.com/maven-profiles> "Guide to Maven Profiles | Baeldung"
[MNG-3328]: <https://issues.apache.org/jira/browse/MNG-3328> "[MNG-3328] Allow multiple profile activation properties. - ASF JIRA"
[MNG-6345]: <https://issues.apache.org/jira/browse/MNG-6345> "[MNG-6345] Support profile activation via script. - ASF JIRA"
[profile-activator-extension]: <https://github.com/random-maven/profile-activator-extension> "random-maven/profile-activator-extension: Provide flexible Maven profile activation via script"
[stackoverflow-5417437]: <https://stackoverflow.com/questions/5417437/activation-of-maven-profile-based-on-multiple-properties> "Activation of maven profile based on multiple properties - Stack Overflow"
[wikipedia-en-mvel]: <https://en.wikipedia.org/wiki/MVEL> "MVEL - Wikipedia"
