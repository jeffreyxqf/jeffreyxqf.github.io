---
layout: post
title:    "Using Jslint to check JavaScript"
date:   2016-08-15 19:40:47 +0800
tags: JavaScript Jslint
categories: JavaScript
---

本文主要介绍了利用jslint来检测JavaScript 的实现是否规范正确的方法





    public static void main(String[] args) throws IOException {
        JSLintBuilder builder = new JSLintBuilder();

        // init with default jslint.js  
        JSLint jsLint = builder.fromDefault();
        // init with yourself defined jslint.js  
        /*JSLint jsLint = builder.fromFile(new File(JSLintTest.class.getResource("/").getPath()
                                                  + "/resource/webjslint.js"));*/
        //add options 
        jsLint.addOption(Option.SLOPPY);
        jsLint.addOption(Option.WHITE);
        jsLint.addOption(Option.EQEQ);
        jsLint.addOption(Option.NOMEN);

        //jsLint.lint(String systemId, Reader reader)
          //the first parameter is the file name to be check, the other is the content of the file 
        JSLintResult result = jsLint.lint("test.js", new FileReader(new File(ValidateScript.class
            .getResource("/").getPath() + "/resource/test.js")));
        
        List<Issue> issues = result.getIssues();
        System.out.println(String.format("Issue Size : %1$d", issues.size()));
        if (issues != null && issues.size() > 0) {
            for (Issue issue : issues) {
                System.out.println(String.format("Row : %1$s, Evidence : %2$s ，Reason：%3$s", issue.getLine(),
                    issue.getEvidence(), issue.getReason()));
            }
        }
    }



About there are some other options : 



	  ANON("If the space may be omitted in anonymous function declarations", Boolean.class), 
	
	  BITWISE("If bitwise operators should be allowed", Boolean.class), 
	
	  BROWSER("If the standard browser globals should be predefined", Boolean.class), 
	
	  CAP("If upper case html should be allowed", Boolean.class), 
	
	  CONTINUE("If the continuation statement should be tolerated", Boolean.class), 
	
	  CSS("If css workarounds should be tolerated", Boolean.class), 
	
	  DEBUG("If debugger statements should be allowed", Boolean.class), 
	
	  DEVEL("If logging should be allowed (console, alert, etc.)", Boolean.class), 
	
	  EQEQ("If == should be allowed", Boolean.class), 
	
	  ES5("If es5 syntax should be allowed", Boolean.class), 
	
	  EVIL("If eval should be allowed", Boolean.class), 
	
	  FORIN("If for in statements need not filter", Boolean.class), 
	
	  FRAGMENT("If html fragments should be allowed", Boolean.class), 
	
	  INDENT("The indentation factor", Integer.class), 
	
	  MAXERR("The maximum number of errors to allow", Integer.class), 
	
	  MAXLEN("The maximum length of a source line", Integer.class), 
	
	  NEWCAP("If constructor names capitalization is ignored", Boolean.class), 
	
	  NODE("If node.js globals should be predefined", Boolean.class), 
	
	  NOMEN("If names may have dangling _", Boolean.class), 
	
	  ON("If html event handlers should be allowed", Boolean.class), 
	
	  PASSFAIL("If the scan should stop on first error", Boolean.class), 
	
	  PLUSPLUS("If increment/decrement should be allowed", Boolean.class), 
	
	  PREDEF("The names of predefined global variables", StringArray.class), 
	
	  PROPERTIES("If all property names must be declared with /*properties*/", Boolean.class), 
	
	  REGEXP("If the . should be allowed in regexp literals", Boolean.class), 
	
	  RHINO("If the rhino environment globals should be predefined", Boolean.class), 
	
	  SLOPPY("If the 'use strict'; pragma is optional", Boolean.class), 
	
	  SUB("If all forms of subscript notation are tolerated", Boolean.class), 
	
	  UNDEF("If variables can be declared out of order", Boolean.class), 
	
	  UNPARAM("If unused parameters should be tolerated", Boolean.class), 
	
	  VARS("If multiple var statements per function should be allowed", Boolean.class), 
	
	  WHITE("If sloppy whitespace is tolerated", Boolean.class), 
	
	  WIDGET("If the yahoo widgets globals should be predefined", Boolean.class), 
	
	  WINDOWS("If ms windows-specific globals should be predefined", Boolean.class);


And there is another option to check the static javascript code, JSHint, you can refer the following Url for details: 

  http://jshint.com/docs/ 

Difference between JSHint & JsLint

  http://cn.tanshuai.com/jshint-vs-jslint/
