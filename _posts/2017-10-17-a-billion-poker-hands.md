---
layout: post
title: 'Playing card enum in Java'
date: '2016-10-17 22:00 -04:00'
author: george
categories: fun
comments: true
---

It's fun to just play with a language. Why be in this business if it isn't fun? So one day I wanted to make a Java enum. I thought - "a deck of cards is the perfect enum."

I made a simple maven project.

    ├── README.md
    ├── pom.xml
    ├── src
    │   ├── main
    │   │   └── java
    │   │       └── com
    │   │           └── tallgeorge
    │   │               └── playingcards
    │   │                   ├── Main.java
    │   │                   └── deck
    │   │                       └── PlayingCardEnum.java
    │   └── test
    │       └── java
    │           └── com
    │               └── tallgeorge
    │                   └── playingcards
    │                       └── deck
    │                           └── PlayingCardTest.java

And it had a simple POM file

 ```
 <?xml version="1.0" encoding="UTF-8"?>
 <project xmlns="http://maven.apache.org/POM/4.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
     <modelVersion>4.0.0</modelVersion>

     <groupId>com.tallgeorge.playingcards</groupId>
     <artifactId>playingcards</artifactId>
     <version>1.0-SNAPSHOT</version>

     <properties>
         <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
     </properties>

     <dependencies>
         <dependency>
             <groupId>junit</groupId>
             <artifactId>junit</artifactId>
             <version>4.12</version>
             <scope>test</scope>
         </dependency>
     </dependencies>

     <build>
         <plugins>
             <plugin>
                 <groupId>org.apache.maven.plugins</groupId>
                 <artifactId>maven-compiler-plugin</artifactId>
                 <version>3.5.1</version>
                 <configuration>
                     <source>1.8</source>
                     <target>1.8</target>
                 </configuration>
             </plugin>
         </plugins>
     </build>

 </project>
 ```

I did add junit because I like to write tests before I write code - it's like buying things on a credit card. You get to have it before you paid for it.

Anyway, I figured an enumerated card should have these useful properties so I can write out its value like "jack club" or J♣

* value - the name of the card, like "three" or "jack"
* suite - the name of the suite, like "club" or "diamond"
* valueabbr - a one character abbreviation of the card value, like "3" or "K"
* symbol - a one character representation of the symbol, like "♣"

Note the use of unicode. Always good to tell maven you are using UTF-8 in the POM file.

Then I wrote a test to see if my enumeration had it right. Assuming the deck is in order (and I'm writing a test, so this is the place to assume), then the 29th card in the deck should be the three of clubs.

```
    package com.tallgeorge.playingcards.deck;

    import org.junit.Test;

    import static org.junit.Assert.assertEquals;

    public class PlayingCardTest {

        @Test
        public void cardIndexCorrect() {
            PlayingCardEnum threeOfClubs = PlayingCardEnum.values()[28];
            assertEquals("Card 28 has correct value", "three", threeOfClubs.getValue());
            assertEquals("Card 28 has correct suite", "club", threeOfClubs.getSuite());
            assertEquals("Card 28 has correct valueabbr", "3", threeOfClubs.getValueAbbr());
            assertEquals("Card 28 has correct symbol", "3♣", threeOfClubs.getSymbol());
        }
    }
```


This is great. I could use my enum to cycle through the deck and print it out. Except when I ran the test it failed because I hadn't paid my credit card bill by writing the code.

```
playingcards $ mvn test
[INFO] Scanning for projects...
[INFO]
[INFO] ------------------------------------------------------------------------
[INFO] Building playingcards 1.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO]
[INFO] --- maven-resources-plugin:2.6:resources (default-resources) @ playingcards ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory /Users/georgecampbell/blog/playingcards/src/main/resources
[INFO]
[INFO] --- maven-compiler-plugin:3.5.1:compile (default-compile) @ playingcards ---
[INFO] Changes detected - recompiling the module!
[INFO]
[INFO] --- maven-resources-plugin:2.6:testResources (default-testResources) @ playingcards ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory /Users/georgecampbell/blog/playingcards/src/test/resources
[INFO]
[INFO] --- maven-compiler-plugin:3.5.1:testCompile (default-testCompile) @ playingcards ---
[INFO] Nothing to compile - all classes are up to date
[INFO]
[INFO] --- maven-surefire-plugin:2.12.4:test (default-test) @ playingcards ---
[INFO] Surefire report directory: /Users/georgecampbell/blog/playingcards/target/surefire-reports

-------------------------------------------------------
 T E S T S
-------------------------------------------------------
Running com.tallgeorge.playingcards.deck.PlayingCardTest
Tests run: 1, Failures: 0, Errors: 1, Skipped: 0, Time elapsed: 0.043 sec <<< FAILURE!
cardIndexCorrect(com.tallgeorge.playingcards.deck.PlayingCardTest)  Time elapsed: 0.008 sec  <<< ERROR!
java.lang.NoClassDefFoundError: com/tallgeorge/playingcards/deck/PlayingCardEnum
        at com.tallgeorge.playingcards.deck.PlayingCardTest.cardIndexCorrect(PlayingCardTest.java:14)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:498)
        at org.junit.runners.model.FrameworkMethod$1.runReflectiveCall(FrameworkMethod.java:50)
        at org.junit.internal.runners.model.ReflectiveCallable.run(ReflectiveCallable.java:12)
        at org.junit.runners.model.FrameworkMethod.invokeExplosively(FrameworkMethod.java:47)
        at org.junit.internal.runners.statements.InvokeMethod.evaluate(InvokeMethod.java:17)
        at org.junit.runners.ParentRunner.runLeaf(ParentRunner.java:325)
        at org.junit.runners.BlockJUnit4ClassRunner.runChild(BlockJUnit4ClassRunner.java:78)
        at org.junit.runners.BlockJUnit4ClassRunner.runChild(BlockJUnit4ClassRunner.java:57)
        at org.junit.runners.ParentRunner$3.run(ParentRunner.java:290)
        at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:71)
        at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:288)
        at org.junit.runners.ParentRunner.access$000(ParentRunner.java:58)
        at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:268)
        at org.junit.runners.ParentRunner.run(ParentRunner.java:363)
        at org.apache.maven.surefire.junit4.JUnit4Provider.execute(JUnit4Provider.java:252)
        at org.apache.maven.surefire.junit4.JUnit4Provider.executeTestSet(JUnit4Provider.java:141)
        at org.apache.maven.surefire.junit4.JUnit4Provider.invoke(JUnit4Provider.java:112)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:498)
        at org.apache.maven.surefire.util.ReflectionUtils.invokeMethodWithArray(ReflectionUtils.java:189)
        at org.apache.maven.surefire.booter.ProviderFactory$ProviderProxy.invoke(ProviderFactory.java:165)
        at org.apache.maven.surefire.booter.ProviderFactory.invokeProvider(ProviderFactory.java:85)
        at org.apache.maven.surefire.booter.ForkedBooter.runSuitesInProcess(ForkedBooter.java:115)
        at org.apache.maven.surefire.booter.ForkedBooter.main(ForkedBooter.java:75)
Caused by: java.lang.ClassNotFoundException: com.tallgeorge.playingcards.deck.PlayingCardEnum
        at java.net.URLClassLoader.findClass(URLClassLoader.java:381)
        at java.lang.ClassLoader.loadClass(ClassLoader.java:424)
        at sun.misc.Launcher$AppClassLoader.loadClass(Launcher.java:331)
        at java.lang.ClassLoader.loadClass(ClassLoader.java:357)
        ... 30 more


Results :

Tests in error:
  cardIndexCorrect(com.tallgeorge.playingcards.deck.PlayingCardTest): com/tallgeorge/playingcards/deck/PlayingCardEnum

Tests run: 1, Failures: 0, Errors: 1, Skipped: 0

[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 1.027 s
[INFO] Finished at: 2017-10-17T22:33:41-04:00
[INFO] Final Memory: 10M/245M
[INFO] ------------------------------------------------------------------------
[ERROR] Failed to execute goal org.apache.maven.plugins:maven-surefire-plugin:2.12.4:test (default-test) on project playingcards: There are test failures.
[ERROR]
[ERROR] Please refer to /Users/georgecampbell/blog/playingcards/target/surefire-reports for the individual test results.
[ERROR] -> [Help 1]
[ERROR]
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR]
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/MojoFailureException

```

With java there's just a lot of drama when things go wrong. It couldn't even find the enum PlayingCardEnum I specified in the test because I hadn't written it yet.

It was time to write the enum!

    package com.tallgeorge.playingcards.deck;

    /**
     * An enumeration representing standard playing cards in as-shipped order.
     */
    public enum PlayingCardEnum {

        ACE_SPADE("ace", "spade", "A", "A♠", 0),
        TWO_SPADE("two", "spade", "2", "2♠", 1),
        THREE_SPADE("three", "spade", "3", "3♠", 2),
        FOUR_SPADE("four", "spade", "4", "4♠", 3),
        FIVE_SPADE("five", "spade", "5", "5♠", 4),
        SIX_SPADE("six", "spade", "6", "6♠", 5),
        SEVEN_SPADE("seven", "spade", "7", "7♠", 6),
        EIGHT_SPADE("eight", "spade", "8", "8♠", 7),
        NINE_SPADE("nine", "spade", "9", "9♠", 8),
        TEN_SPADE("ten", "spade", "10", "10♠", 9),
        JACK_SPADE("jack", "spade", "J", "J♠", 10),
        QUEEN_SPADE("queen", "spade", "Q", "Q♠", 11),
        KING_SPADE("king", "spade", "K", "K♠", 12),

        ACE_DIAMOND("ace", "diamond", "A", "A♦", 0),
        TWO_DIAMOND("two", "diamond", "2", "2♦", 1),
        THREE_DIAMOND("three", "diamond", "3", "3♦", 2),
        FOUR_DIAMOND("four", "diamond", "4", "4♦", 3),
        FIVE_DIAMOND("five", "diamond", "5", "5♦", 4),
        SIX_DIAMOND("six", "diamond", "6", "6♦", 5),
        SEVEN_DIAMOND("seven", "diamond", "7", "7♦", 6),
        EIGHT_DIAMOND("eight", "diamond", "8", "8♦", 7),
        NINE_DIAMOND("nine", "diamond", "9", "9♦", 8),
        TEN_DIAMOND("ten", "diamond", "10", "10♦", 9),
        JACK_DIAMOND("jack", "diamond", "J", "J♦", 10),
        QUEEN_DIAMOND("queen", "diamond", "Q", "Q♦", 11),
        KING_DIAMOND("king", "diamond", "K", "K♦", 12),

        ACE_CLUB("ace", "club", "A", "A♣", 0),
        TWO_CLUB("two", "club", "2", "2♣", 1),
        THREE_CLUB("three", "club", "3", "3♣", 2),
        FOUR_CLUB("four", "club", "4", "4♣", 3),
        FIVE_CLUB("five", "club", "5", "5♣", 4),
        SIX_CLUB("six", "club", "6", "6♣", 5),
        SEVEN_CLUB("seven", "club", "7", "7♣", 6),
        EIGHT_CLUB("eight", "club", "8", "8♣", 7),
        NINE_CLUB("nine", "club", "9", "9♣", 8),
        TEN_CLUB("ten", "club", "10", "10♣", 9),
        JACK_CLUB("jack", "club", "J", "J♣", 10),
        QUEEN_CLUB("queen", "club", "Q", "Q♣", 11),
        KING_CLUB("king", "club", "K", "K♣", 12),

        ACE_HEART("ace", "heart", "A", "A♥", 0),
        TWO_HEART("two", "heart", "2", "2♥", 1),
        THREE_HEART("three", "heart", "3", "3♥", 2),
        FOUR_HEART("four", "heart", "4", "4♥", 3),
        FIVE_HEART("five", "heart", "5", "5♥", 4),
        SIX_HEART("six", "heart", "6", "6♥", 5),
        SEVEN_HEART("seven", "heart", "7", "7♥", 6),
        EIGHT_HEART("eight", "heart", "8", "8♥", 7),
        NINE_HEART("nine", "heart", "9", "9♥", 8),
        TEN_HEART("ten", "heart", "10", "10♥", 9),
        JACK_HEART("jack", "heart", "J", "J♥", 10),
        QUEEN_HEART("queen", "heart", "Q", "Q♥", 11),
        KING_HEART("king", "heart", "K", "K♥", 12);

        /**
         * A string representation of the value.
         */
        private final String value;

        /**
         * A string representation of the suite.
         */
        private final String suite;

        /**
         * A string representation of the value, shortened.
         */
        private final String valueAbbr;

        /**
         * A string representation of the value and the suite, shortened.
         */
        private final String symbol;

        /**
         * A playing card.
         * @param value     a string representation of the value.
         * @param suite     a string representation of the suite.
         * @param valueAbbr a string representation of the value, shortened.
         * @param symbol    a string representation of the value, shortened and the symbol.
         */
        PlayingCardEnum(String value, String suite, String valueAbbr, String symbol, int rank) {
            this.value = value;
            this.suite = suite;
            this.valueAbbr = valueAbbr;
            this.symbol = symbol;
        }

        /**
         * Get the string representation of the value.
         * @return string, representation of the value.
         */
        public String getValue() {
            return value;
        }

        /**
         * Get the string representation of the suites.
         * @return string, representation of the suites.
         */
        public String getSuite() {
            return suite;
        }

        /**
         * Get the string representation of the value, shortened.
         * @return string, representation of the value, shortened.
         */
        public String getValueAbbr() {
            return valueAbbr;
        }

        /**
         * Get the string representation of the value and the symbol.
         * @return string, representation of the value, shortened and the symbol.
         */
        public String getSymbol() {
            return symbol;
        }

        @Override
        public String toString() {
            return String.format("%3s", this.getSymbol());
        }
    }

Now you see this is where java shows some of its warts. There's a lot of ceremony required to create a simple object. But at least everything is spelled out so you can see it, and autocomplete on the IDE takes the drudgery out of it.

Anyway, let's run this test and see what happens.

```
[INFO] Scanning for projects...
[INFO]
[INFO] ------------------------------------------------------------------------
[INFO] Building playingcards 1.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO]
[INFO] --- maven-resources-plugin:2.6:resources (default-resources) @ playingcards ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory /Users/georgecampbell/blog/playingcards/src/main/resources
[INFO]
[INFO] --- maven-compiler-plugin:3.5.1:compile (default-compile) @ playingcards ---
[INFO] Nothing to compile - all classes are up to date
[INFO]
[INFO] --- maven-resources-plugin:2.6:testResources (default-testResources) @ playingcards ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory /Users/georgecampbell/blog/playingcards/src/test/resources
[INFO]
[INFO] --- maven-compiler-plugin:3.5.1:testCompile (default-testCompile) @ playingcards ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 1 source file to /Users/georgecampbell/blog/playingcards/target/test-classes
[INFO]
[INFO] --- maven-surefire-plugin:2.12.4:test (default-test) @ playingcards ---
[INFO] Surefire report directory: /Users/georgecampbell/blog/playingcards/target/surefire-reports

-------------------------------------------------------
 T E S T S
-------------------------------------------------------
Running com.tallgeorge.playingcards.deck.PlayingCardTest
Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.041 sec

Results :

Tests run: 1, Failures: 0, Errors: 0, Skipped: 0

[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 1.319 s
[INFO] Finished at: 2017-10-17T22:30:51-04:00
[INFO] Final Memory: 16M/209M
[INFO] ------------------------------------------------------------------------
```

So there you have it - an enumerated deck of cards in java with a test. So why didn't I test more cards? Because I'm writing this for fun. But I should. Heck, the cards should go in a properties file. But never mind right now. Remember, you have to start somewhere. In the next blog, I'll create a deck.

