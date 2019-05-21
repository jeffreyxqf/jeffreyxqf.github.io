---

layout: post
title: 2019-05-07-Export DB records to Excel files Using Annotaion and Reflection 
tags: Java 
category: Java
comments: true

---

最近项目需要将mysql数据导出至Excel文件中，通过查阅相关资料以及不断尝试总结，利用了Apache POI API通过反射以及注解方式实现，运行良好。本文主要通过一个简单的进行说明。







- 定义注解接口


```java
/**
 * @Author: xqf
 * @Date: 2019/5/5 15:03
 */

@Retention(RetentionPolicy.RUNTIME)
public @interface Annotation {

    /**
     * 列索引
     *
     * @return
     */
    int columnIndex() default 0;

    /**
     * 列名
     *
     * @return
     */
    String columnName() default "";

    /**
     * 默认值
     */
    String defaultValue() default "";
}
```



- 定义Excel实体类



  ```java

public class ExcelEntity {
	
	public ExcelEntity() {
	}
	
	public ExcelEntity(int id, String name) {
		this.id = id;
		this.name = name;
	}
   @Annotation(columnIndex = 0, columnName = "序号")
	private int id;
   @Annotation(columnIndex = 1, columnName = "名称",defaultValue = "xqf")
	private String name;
	
	public int getId() {
		return id;
	}
	public void setId(int id) {
		this.id = id;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}
}
  ```





- Excel创建实现

```java
public class GenerateExcel {

    public static void outExcelFile(List<?> data, String path) {

        File file = new File(path);
        HSSFWorkbook wb = new HSSFWorkbook();
        Sheet sheet = wb.createSheet("sheel");
        Row row = sheet.createRow(0);
        HSSFCellStyle style = wb.createCellStyle();
        style.setAlignment(HorizontalAlignment.CENTER);
        Field[] fields = data.get(0).getClass().getDeclaredFields();
        int index = 0;
        String name = "";
        Annotation myAnnotation;
        for (Field f : fields) {
            if (f.isAnnotationPresent(Annotation.class)) {
                myAnnotation = f.getAnnotation(Annotation.class);
                index = myAnnotation.columnIndex();
                name = myAnnotation.columnName();
                creCell(row, index, name, style);
            }
        }

        int rowIndex = 1;
        for (Object obj : data) {
            row = sheet.createRow(rowIndex++);
            for (Field f : fields) {
                f.setAccessible(true);
                if (f.isAnnotationPresent(Annotation.class)) {
                    myAnnotation = f.getAnnotation(Annotation.class);
                    index = myAnnotation.columnIndex();
                    try {
                        creCell(row, index, String.valueOf(f.get(obj)), style);
                    } catch (IllegalArgumentException e) {
                        e.printStackTrace();
                    } catch (IllegalAccessException e) {
                        e.printStackTrace();
                    }
                }
            }
        }

        FileOutputStream outputStream = null;
        try {
            outputStream = new FileOutputStream(file);
            wb.write(outputStream);
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                if (wb != null) {
                    try {
                        wb.close();
                    } catch (IOException e) {
                        e.printStackTrace();
                    }
                }
                if (outputStream != null) {
                    outputStream.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
    
    private static void creCell(Row row, int c, String cellValue, CellStyle style) {
        Cell cell = row.createCell(c);
        cell.setCellValue(cellValue);
        cell.setCellStyle(style);
    }

```



note ： 因项目数据量较大可以达到几十甚至数百万条记录，而且需要生成至一个sheet中。而HSSFWorkbook 主要是用来操作Excel2003以前（包括2003）的版本，扩展名是.xls，一个sheet中最大支持65535条记录。推荐所用SXSSF API，其支持2007以后的版本，记录数量级较大，占用内存较小。



<https://poi.apache.org/components/spreadsheet/>

