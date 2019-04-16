# PSD2 Berlin Group Serverside API generation 

We are generating a serverside Spring API for a real world API standard. There are some issues in the generation which we worked around in V3.3.4 but it breaks in 4.0.0beta-3.
I created this repo as an example of a complex (real-world) API and to get some suggestions.
I am worried that as we start to implement more functionality the generated code may be incorrect (we get duplicate parameters and multiple schema warnings).  

Ideally we would not change the API definition, it is a third party standard.

```
https://www.berlin-group.org/nextgenpsd2-downloads
```

## Project setup
To switch between V3.3.4 and 4.0.0beta-3 the pom.xml can be changed.
the output of mvnw clean install for each version is here: 

[Maven Output 3.3.4](./mvn-3.3.4.txt)

[Maven Output 4.0.0-beta3](./mvn-4.0.0-beta3.txt)

pom versions: 
``` xml 
<!-- <openapi-generator.version>3.3.4</openapi-generator.version> -->
     <openapi-generator.version>4.0.0-beta3</openapi-generator.version>
```

to build run 
``` bash
mvnw clean install
```


# Issues of generation

## 4.0.0-beta3

### Polymorphic requests/responses 
Polymorphic requests/responses of creates class names such as OneOfobjectupdatePsuAuthenticationselectPsuAuthenticationMethodtransactionAuthorisation which are not generated.
(I created these manually to allow compilation)
Personally I don't think this is good API design but we are stuck with it.

### Property initialization  
This means the project does not compile:
for example 
```java  
  @JsonProperty("_links")
  private LinksAll links = new HashMap<>();
```   
In V3.3.4 this was initialized to null and so was OK.


### Duplicate parameters
Warnings about duplicate parameters. I checked a few of these and they don't appear to be duplicates.
Issue:
<https://github.com/OpenAPITools/openapi-generator/issues/2631>


```
[INFO] --- openapi-generator-maven-plugin:4.0.0-beta3:generate (ibis-psd2) @ psd2 ---
[WARNING] There were issues with the specification, but validation has been explicitly disabled.
Errors: 
	-attribute paths.'/v1/consents/{consentId}'(delete).parameters.There are duplicate parameter values
	-attribute paths.'/v1/consents/{consentId}/authorisations'(get).parameters.There are duplicate parameter values
	-attribute paths.'/v1/accounts/{account-id}/transactions/{transactionId}'(get).parameters.There are duplicate parameter values
	-attribute paths.'/v1/consents/{consentId}/authorisations/{authorisationId}'(get).parameters.There are duplicate parameter values
	-attribute paths.'/v1/signing-baskets/{basketId}/authorisations'(post).parameters.There are duplicate parameter values
... many more
``` 

## 3.3.4
The generated code referenced a class with name UNKNOWN_BASE_TYPE.
Issue:
<https://github.com/OpenAPITools/openapi-generator/issues/2236>
We could get 3.3.4 to compile and run by ignoring validations and by creating a class UNKNOWN_BASE_TYPE:

``` java
package com.example.psd2.rest.model;
import java.util.HashMap;
public class UNKNOWN_BASE_TYPE extends HashMap {
}
```
 

### Multiple schemas warnings in 3.3.4 and 4.0.0-beta3
Many warnings for this but seems to generate OK. Can be ignored or avoided with configuration?  
```
[WARNING] Multiple schemas found in content, returning only the first one
[WARNING] Multiple schemas found in content, returning only the first one
... many more
```
