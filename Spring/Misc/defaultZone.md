In Spring Boot we could use camelCase or hyphenated-keys but for the word `defaultZone` (Eureka) it seems only camel case works

StackOverflow answer: 

>"Ok, got it... the label after `service-url` property (which can be aliased as `serviceUrl` in YML) is a HashMap KEY, not a property label. 
>So it has to be kept as a Camel Case tag in any ways!"