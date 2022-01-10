# JAVA-POI-Excel操作教程(Java操作Excel文档)

POI在JAVA中主要是指Apache POI，它是一种开源的API，它允许程序员使用Java程序创建,修改和显示MS Office文件。

目前JAVAWEB项目中免不了进行一些数据导出文档、从文档导入数据这些操作，利用POI即可对其数据进行批量导入、导出为Office文件（Word、Excel等等）。

这里我们来介绍一下操作Excel，因为大部分业务操作都已输出Excel为主。

这里我并不打算大家使用原生的Apache POI，它的操作目前繁琐而且需要一定基础。下面介绍两款第三方的开源POI组件，也是目前最国内项目常用的：EasyPoi、EasyExcel。

## EasyPoi

导入最新的Maven依赖：

```xml
<dependency>
   <groupId>cn.afterturn</groupId>
   <artifactId>easypoi-spring-boot-starter</artifactId>
   <version>4.4.0</version>
</dependency>
```

### 操作工具类:

```java
/**
 * Excel操作工具类
 * @author zty
 */
public class ExcelUtils {
    private static final String HSSF  = ".xls";
    private static final String XSSF  = ".xlsx";

    /**
     * 导出List<Map>数据到Excel
     * @param model
     * @param request
     * @param response
     * @throws Exception
     */
    @SuppressWarnings("unchecked")
   public static void exportMapDataToExcel(Map<String, Object> model, HttpServletRequest request,
                                           HttpServletResponse response) throws Exception {
        String codedFileName = "临时文件";
        Workbook workbook = ExcelExportUtil.exportExcel(
            (ExportParams) model.get(MapExcelConstants.PARAMS),
            (List<ExcelExportEntity>) model.get(MapExcelConstants.ENTITY_LIST),
            (Collection<? extends Map<?, ?>>) model.get(MapExcelConstants.MAP_LIST));
        if (model.containsKey(MapExcelConstants.FILE_NAME)) {
            codedFileName = (String) model.get(MapExcelConstants.FILE_NAME);
        }
        if (workbook instanceof HSSFWorkbook) {
            codedFileName += HSSF;
        } else {
            codedFileName += XSSF;
        }
        if (isIE(request)) {
            codedFileName = java.net.URLEncoder.encode(codedFileName, "UTF8");
        } else {
            codedFileName = new String(codedFileName.getBytes("UTF-8"), "ISO-8859-1");
        }
        response.setHeader("content-disposition", "attachment;filename=" + codedFileName);
        ServletOutputStream out = response.getOutputStream();
        workbook.write(out);
        out.flush();
    }
    
    /**
     * 导出注解类数据到Excel(不支持多sheet)
     * @param model
     * @param request
     * @param response
     * @throws Exception
     */
    @SuppressWarnings("unchecked")
   public static void exportDataToSingleExcel(Map<String, Object> model, HttpServletRequest request,
                                           HttpServletResponse response) throws Exception {
        String codedFileName = "临时文件";
        Workbook workbook = null;
        
      workbook = ExcelExportUtil.exportExcel((ExportParams) model.get(NormalExcelConstants.PARAMS),
            (Class<?>) model.get(NormalExcelConstants.CLASS),
            (Collection<?>) model.get(NormalExcelConstants.DATA_LIST));
      
        if (model.containsKey(NormalExcelConstants.FILE_NAME)) {
            codedFileName = (String) model.get(NormalExcelConstants.FILE_NAME);
        }
        if (workbook instanceof HSSFWorkbook) {
            codedFileName += HSSF;
        } else {
            codedFileName += XSSF;
        }
        if (isIE(request)) {
            codedFileName = java.net.URLEncoder.encode(codedFileName, "UTF8");
        } else {
            codedFileName = new String(codedFileName.getBytes("UTF-8"), "ISO-8859-1");
        }
        response.setHeader("content-disposition", "attachment;filename=" + codedFileName);
        ServletOutputStream out = response.getOutputStream();
        workbook.write(out);
        out.flush();
    }
    
    /**
     * 导出Excel模板
     * @param model
     * @param request
     * @param response
     * @throws Exception
     */
    @SuppressWarnings({ "unchecked", "deprecation" })
   public static void exportExcelTemplate(Map<String, Object> model, HttpServletRequest request,
                                           HttpServletResponse response) throws Exception {
        String codedFileName = "临时文件";
        Workbook workbook = ExcelExportUtil.exportExcel(
            (TemplateExportParams) model.get(TemplateExcelConstants.PARAMS),
            (Class<?>) model.get(TemplateExcelConstants.CLASS),
            (List<?>) model.get(TemplateExcelConstants.LIST_DATA),
            (Map<String, Object>) model.get(TemplateExcelConstants.MAP_DATA));
        if (model.containsKey(NormalExcelConstants.FILE_NAME)) {
            codedFileName = (String) model.get(NormalExcelConstants.FILE_NAME);
        }
        if (workbook instanceof HSSFWorkbook) {
            codedFileName += HSSF;
        } else {
            codedFileName += XSSF;
        }
        if (isIE(request)) {
            codedFileName = java.net.URLEncoder.encode(codedFileName, "UTF8");
        } else {
            codedFileName = new String(codedFileName.getBytes("UTF-8"), "ISO-8859-1");
        }
        response.setHeader("content-disposition", "attachment;filename=" + codedFileName);
        ServletOutputStream out = response.getOutputStream();
        workbook.write(out);
        out.flush();
    }
    
    /**
     * 浏览器检测
     * @param request
     * @return
     */
    private static boolean isIE(HttpServletRequest request) {
        return (request.getHeader("USER-AGENT").toLowerCase().indexOf("msie") > 0 || request
            .getHeader("USER-AGENT").toLowerCase().indexOf("rv:11.0") > 0) ? true : false;
    }
   
}
```

### 使用案例

#### 定义类属性

对于需要进行导出、导入的数据类，需要进行设置相关属性，否则EasyPoi有无法进行解析。

```
public class Article {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Integer id;

    @Column(name = "article_type")
    @Excel(name = "文章类型",replace={"新闻_1","科研动态_2","中心事务_3"},isImportField="true")
    private Integer articleType;

    @Excel(name = "标题",orderNum = "2",width = 50,isImportField="true")
    private String title;
}    
```

其中对于需要定义的解析属性，添加@Excel注解，它的常用属性为：

> name： 列名
>
> orderNum：排序
>
> width：列宽度
>
> format：日期格式化，如"yyyy-MM-dd"
>
> replace：替换内容，常用于定义的数据类型，其中格式为 “新文本_被替换文本”



#### 导出操作

```java
 /**
 * 将所有文章导出为Excel文件
 */
  @GetMapping("export")
  @ApiOperation(value = "导出文章", notes = "导出文章")
  public void exportExcel(HttpServletRequest request, HttpServletResponse response) throws Exception {
    List<Article> articles = articleService.findAll();

    Map<String, Object> model = new HashMap<String,Object>();
	SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");

	model.put(NormalExcelConstants.FILE_NAME,"文章列表");
	model.put(NormalExcelConstants.CLASS,Article.class);
	model.put(NormalExcelConstants.PARAMS,new ExportParams("文章列表","导出时间:"+sdf.format(new Date()),"导出信息"));
	model.put(NormalExcelConstants.DATA_LIST,articles);
	ExcelUtils.exportDataToSingleExcel(model, request, response);
  }
```

其中我们创建了一个HashMap用来定义表头，泛型为<String,Object>， 其中：

> NormalExcelConstants.FILE_NAME ： 导出文件名
>
> NormalExcelConstants.CLASS：导出数据的类型
>
> NormalExcelConstants.PARAMS ：导出的参数
>
> NormalExcelConstants.DATA_LIST： 导出的数据组

#### 导入操作

所谓的导入操作就是上传excel文件，通过解析excel文档然后将其导入为实体数据的操作。

***需要注意的是，导入操作上传的excel文档格式必须和设置的模板格式一致，EasyPoi才从正常解析它们，否则就会报错。***

1.所以我们得先需要给用户导出一个模板Excel文件：

```java
/**
 * 下载导入模板
 */
@GetMapping("template")
@ApiOperation(value = "下载导入模板", notes = "下载导入模板")
public void exportTemplate(HttpServletRequest request, HttpServletResponse response) throws Exception {
    Map<String, Object> model = new HashMap<String,Object>();
    SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");

    model.put(NormalExcelConstants.FILE_NAME,"文章导入模板");
    model.put(NormalExcelConstants.CLASS,Article.class);
    model.put(NormalExcelConstants.PARAMS,new ExportParams("文章列表","导出时间:"+sdf.format(new Date()),"导出信息"));
    model.put(NormalExcelConstants.DATA_LIST,new ArrayList());
    ExcelUtils.exportDataToSingleExcel(model, request, response);
}
```

其中的操作和上面的上传基本上一致，但是这里由于只是模板，不需要导出对应类数据，所以*NormalExcelConstants.DATA_LIST* 设置为空。



2.用户对该模板进行相关新增操作后，随后上传导入：

```java
/**
  * 导入
  */
@PostMapping(value="import")
@ResponseBody
@ApiOperation(value = "导入文章", notes = "导入文章")
public Result importExcel(MultipartFile files) throws Exception{
       // 获取上传文件对象
      MultipartFile file = files;
      ImportParams params = new ImportParams();
      params.setTitleRows(2);
      params.setHeadRows(1);
      params.setNeedSave(false);
      try {
         List<Article> articles = ExcelImportUtil.importExcel(file.getInputStream(),Article.class,params);
         for (Article article : articles) {
            articleService.save(article);
         }
      } catch (Exception e) {
         throw new Exception("导入数据错误");
      }finally{
         try {
            file.getInputStream().close();
         } catch (IOException e) {
            e.printStackTrace();
         }
      }
   return ResultBuilder.successResult("导入数据成功!");
}
```

这里的ImportParams 用来定义其模板的设置的对应行数，用来识别对应哪些行是数据 ，哪些行是表头。而这里的ExcelImportUtil.importExcel()方法创建出对应的excel的数据组，随后使用Foreach来循环添加至数据库即可（当然如果你有组添加更好）。



## EasyExcel

EasyExcel是Alibaba的一个开源项目，它基于Java的简单、省内存的读写Excel的开源项目。在尽可能节约内存的情况下支持读写百M的Excel。相比于其他Poi操作，它更加简单实用。

这里我们直接使用pig4cloud 下的**[excel-spring-boot-starter](https://github.com/pig-mesh/excel-spring-boot-starter)**项目，它是基于EasyExcel的SpringBoot快速整合插件。

```xml
<dependency>
   <groupId>com.pig4cloud.excel</groupId>
   <artifactId>excel-spring-boot-starter</artifactId>
   <version>1.0.0</version>
</dependency>
```

### 使用案例

它的操作更加简单。

和大多数Poi一样，首先要导出或者导入操作的类进行定义其相关属性（如果不设置，默认为全部输出，列名为属性名）：

```java
@ApiModel("系统日志实体类")
@Table(name = "sys_log")
public class SysLog {
    @ApiModelProperty("日志id")
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @ExcelProperty(value="日志ID",index = 0)
    private Integer id;

    /**
     * 操作人ID
     */
    @ApiModelProperty("操作人ID")
    @Column(name = "user_id")
    @ExcelProperty(value="管理员ID",index = 1)
    private Integer userId;
    
    ...
}
```

这里必须使用了@ExcelProperty注解，其中它接受这些属性：

> value ： 表列名
>
> index ：排序，从0开始最靠前。 不写的话，按先后顺序排序

它还支持一些其他注解：

@ColumnWidth ： 定义列的长度，接受int值

@ExcelIgnore ： 忽略该数据

@ContentStyle ：设置内容的风格，包括背景色、文本颜色



设置类相关输出属性完后，我们只需要在需要导出操作的方法，上使用@ResponseExcel注解（其中name为导出名称、sheets中可以设置为多个sheet，password为加密密码），这里的password对于非加密可以省略。

```java
@ResponseExcel(name = "SysLog", sheets = @Sheet(sheetName = "系统日志"),password = "12345600")
@GetMapping("/output")
public List<SysLog> outputData() {
    List<SysLog> list = sysLogService.findAll();
    return list;
}
```



导入操作：

导入操作则更加简单了，直接在Controller接口请求中使用 @RequestExcel注解即可：

```java
@PostMapping("/upload")
public void upload(@RequestExcel List<DemoData> dataList, BindingResult bindingResult) {
  // JSR 303 校验通用校验获取失败的数据
  List<ErrorMessage> errorMessageList = (List<ErrorMessage>) bindingResult.getTarget();
}
```

@RequestExcel本身就是一个MultipartFile上传效果，所以可以直接用来上传。BindingResult的getTarget可以用来得到操作错误的信息。

这些是最简单的读写操作，关于更多操作可以去Github中康康：https://github.com/pig-mesh/excel-spring-boot-starter/blob/master/README.md



