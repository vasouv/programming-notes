
Example in Spring Boot on how to use the Strategy Pattern to download files.

## Strategy Interface
```java
public interface FileDownloadStrategy {  
  
    String download(String filename);  
  
}
```
## Image Implementation
```java
@Component  
public class ImageDownload implements FileDownloadStrategy {  
  
    @Override  
    public String download(String filename) {  
        return "Downloading image: " + filename;  
    }  
  
}
```
## Video Implementation with extra implementation
This implementation uses an extra service for additional work, specifically for this case.
```java
@Component  
public class VideoDownload implements FileDownloadStrategy {  
  
    private final VideoCompressionService videoCompressionService;  
  
    public VideoDownload(VideoCompressionService videoCompressionService) {  
        this.videoCompressionService = videoCompressionService;  
    }  
  
    @Override  
    public String download(String filename) {  
        return "Downloading video: " + videoCompressionService.compress(filename);  
    }  
  
}
```
### Extra implementation for video
```java
@Service  
public class VideoCompressionService {  
  
    public String compress(String filename) {  
        String[] split = filename.split("\\.");  
        return split[0] + "-COMPRESSED-" + split[1];  
    }  
  
}
```
## File Download Service switching the strategy
```java
@Service  
public class FileDownloadService {  
  
    private final ImageDownload imageDownload;  
    private final VideoDownload videoDownload;  
  
    public FileDownloadService(ImageDownload imageDownload, VideoDownload videoDownload) {  
        this.imageDownload = imageDownload;  
        this.videoDownload = videoDownload;  
    }  
  
    public String download(String filename) {  
  
        FileDownloadStrategy strategy = imageDownload;  
  
        if (filename.startsWith("VID")) strategy = videoDownload;  
  
        return strategy.download(filename);  
  
    }  
}
```
## File Controller
```java
@RestController  
@RequestMapping("download")  
public class FileDownloadController {  
  
    private final FileDownloadService downloadService;  
  
    public FileDownloadController(FileDownloadService downloadService) {  
        this.downloadService = downloadService;  
    }  
  
    @GetMapping  
    public String download(@RequestParam String filename) {  
        return downloadService.download(filename);  
    }  
  
}
```