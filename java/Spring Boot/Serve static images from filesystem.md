How to serve static images from a filesystem directory. The images can be uploaded and stored there, then a custom URL will be created for the images to be fetched.
```properties
server.servlet.context-path=/myproject

file.upload-dir=/opt/myproject
```
The images are uploaded in a hierarchy like so: `/opt/myproject/<category>/<entityId>`. So we have something like: `<upload>/buildings/hq/<files>` or `<upload>/people/vasouv/<files>`.
## Configure resource handler for the directory
```java
@Configuration  
public class WebConfig implements WebMvcConfigurer {  
  
    private final String uploadDir;  
  
    public WebConfig(@Value("${file.upload-dir}") String uploadDir) {  
        this.uploadDir = uploadDir;  
    }  
  
    @Override  
    public void addResourceHandlers(ResourceHandlerRegistry registry) {  
        registry  
                .addResourceHandler("/images/**")  
                .addResourceLocations("file:%s/".formatted(uploadDir));  
    }  
}
```
This configures the handler `/images` under the context path from which all images will be served. Therefore a link `http://localhost:8080/myproject/images/buildings/hq/hq_logo.png` will fetch the image.

Now all images are available and if we know the structure of the directory, we have access to them.

## Create the image URI
Depending on the implementation and how the images will be available, a URI builder is a good approach if we want to set the URI in a DTO.
```java
@Component  
public class URIBuilder {  
  
    private final String appUrl;  
    private final String contextPath;  
  
    public URIBuilder(@Value("${app.url}") String appUrl,  
                      @Value("${server.servlet.context-path}") String contextPath) {  
        this.appUrl = appUrl;  
        this.contextPath = contextPath;  
    }  
  
    public String createBuildingImage(String buildingId, String filename) {  
        return UriComponentsBuilder  
                .fromUri(URI.create("%s/%s".formatted(appUrl, contextPath)))  
                .path("/images/buildings")  
                .path("/{buildingId}")  
                .path("/{filename}")  
                .buildAndExpand(buildingId, filename)  
                .toUriString();  
    }  
  
    public String createPersonImage(String personId, String filename) {  
        return UriComponentsBuilder  
                .fromUri(URI.create("%s/%s".formatted(appUrl, contextPath)))  
                .path("/images/people")  
                .path("/{personId}")  
                .path("/{filename}")  
                .buildAndExpand(personId, filename)  
                .toUriString();  
    }  
}
```
## Unit test the URI builder
```java
class URIBuilderTest {  
  
    private final URIBuilder uriBuilder = new URIBuilder("http://localhost:8080", "/myproject");  
      
    private final String buildingResult = "http://localhost:8080/myproject/images/buildings/hq/hq_logo.png";  
    private final String personResult = "http://localhost:8080/myproject/images/people/vasouv/vasouv_profile.png";  
  
    @Test  
    void createBuildingImage() {  
        var buildingId = "hq";  
        var filename = "hq_logo.png";  
        var buildingImage = uriBuilder.createBuildingImage(buildingId, filename);  
        Assertions  
                .assertThat(buildingImage)  
                .isEqualTo(buildingResult);  
    }  
  
    @Test  
    void createPersonImage() {  
        var personId = "vasouv";  
        var filename = "vasouv_profile.png";  
        var personImage = uriBuilder.createPersonImage(personId, filename);  
        Assertions  
                .assertThat(personImage)  
                .isEqualTo(personResult);  
    }  
}
```