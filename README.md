# spring-java-pass-file-between-apis-to-download-

##API1 SEND FILE
```java
response.setContentType("text/plain;charset=utf-8");
response.setCharacterEncoding("ISO-8859-1");

String reportName = new StringBuffer().append("fileName").append(".txt").toString();
response.setHeader("Content-disposition",new StringBuffer().append("attachment; filename=\"").append(reportName).append("\"").toString());
InputStream in = new ByteArrayInputStream(ficheros.getArchivoByte());

OutputStream out = response.getOutputStream();
byte[] buffer = new byte[1024];
int len;
while ((len = in.read(buffer)) != -1) {
  out.write(buffer, 0, len);
}
out.flush();
```


##API2 RECIVE FILE
```java
// Añadimos los campos que van por parametros
UriComponentsBuilder builder = UriComponentsBuilder.fromHttpUrl(serviceUrl.toString())
    .queryParam("param1", param1);

ResponseEntity<byte[]> entityString = null; 
entityString = restTemplate.exchange(
    builder.build().encode().toUri(), 
    HttpMethod.GET, 
    httpEntity, 
    byte[].class);

ObjectMapper mapper = new ObjectMapper();
if(entityString.getStatusCode() == HttpStatus.OK) {

  response.setContentType("text/plain;charset=utf-8");
  response.setCharacterEncoding("ISO-8859-1");

  String reportName = new StringBuffer().append("fileName").append(".txt").toString();
  response.setHeader("Content-disposition",new StringBuffer().append("attachment; filename=\"").append(reportName).append("\"").toString());

  OutputStream os = response.getOutputStream();
  try (InputStream fis = new ByteArrayInputStream(entityString.getBody());
      InputStream bis = new BufferedInputStream(fis)) {
    byte[] buffer = new byte[4096];
    int n;
    while ((n = bis.read(buffer)) >= 0) {
      os.write(buffer, 0, n);
    }
    os.flush();
    os.close();
  } catch (Exception e) {
    log.error("Error al traspasar el fichero", e);
  }	
}
```
