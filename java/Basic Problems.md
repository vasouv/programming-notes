## Distinct list of elements
Assuming a variable number of concatenated strings that contain country names, retrieve a list of the distinct country names
```java
public List<String> distinct(String... countries) {  
  
    return Arrays.stream(countries)  
            .filter(Objects::nonNull)  
            .filter(country -> !country.isBlank())  
            .flatMap(country -> Arrays.stream(country.split(",")))  
            .map(String::trim)  
            .distinct()  
            .sorted()  
            .toList();  
  
}
```
The code can be improved if Apache Commons Lang is used.
```java
@Test  
void distinct() {  
  
    String valid = "GRC,ESP";  
    String valid2 = "FRA,GRC, NLD";  
    String invalid = "";  
    String invalid2 = null;  
      
    List<String> expected = List.of("ESP", "FRA", "GRC", "NLD");  
  
    List<String> result = countryService.distinct(valid, valid2, invalid, invalid2);  
  
    Assertions.assertThatCollection(result).isEqualTo(expected);  
  
}
```