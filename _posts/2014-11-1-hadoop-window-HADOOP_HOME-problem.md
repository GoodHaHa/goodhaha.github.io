---
layout: post
author: sun
title: "WIN7下运行hadoop程序报：Failed to locate the winutils binary in the hadoop binary path"
date: 2014-11-1 21:14:50
categories: something
---

## The Data Revolution Speaker 讲座记录
WIN7下运行hadoop程序报：Failed to locate the winutils binary in the hadoop binary path

之前在mac上调试hadoop程序（mac之前配置过hadoop环境）一直都是正常的。因为工作需要，需要在windows上先调试该程序，然后再转到linux下。程序运行的过程中，报 Failed to locate the winutils binary in the hadoop binary path    java.io.IOException: Could not locate executable null \bin\winutils.exe in the Hadoop binaries.   

通过断点调试、查看源码发现程序需要根据HADOOP_HOME找到winutils.exe,由于win机器并没有配置该环境变量，所以程序报 null\bin\winutils.exe。

private static String checkHadoopHome() {
  // first check the Dflag hadoop.home.dir with JVM scope
  String home = System.getProperty("hadoop.home.dir");

  // fall back to the system/user-global env variable
  if (home == null) {
    home = System.getenv("HADOOP_HOME");
  }
  try {
     // couldn't find either setting for hadoop's home directory
     if (home == null) {
       throw new IOException("HADOOP_HOME or hadoop.home.dir are not set.");
     }
     if (home.startsWith("\"") && home.endsWith("\"")) {
       home = home.substring(1, home.length()-1);
     }
     // check that the home setting is actually a directory that exists
     File homedir = new File(home);
     if (!homedir.isAbsolute() || !homedir.exists() || !homedir.isDirectory()) {
       throw new IOException("Hadoop home directory " + homedir
         + " does not exist, is not a directory, or is not an absolute path.");
     }
     home = homedir.getCanonicalPath();
  } catch (IOException ioe) {
    if (LOG.isDebugEnabled()) {
      LOG.debug("Failed to detect a valid hadoop home directory", ioe);
    }
    home = null;
  }    
  return home;
}



private static String HADOOP_HOME_DIR = checkHadoopHome (); 
public static final String getQualifiedBinPath(String executable) 
throws IOException {
  // construct hadoop bin path to the specified executable
  String fullExeName = HADOOP_HOME_DIR + File.separator + "bin" 
    + File.separator + executable;

  File exeFile = new File(fullExeName);
  if (!exeFile.exists()) {
    throw new IOException("Could not locate executable " + fullExeName
      + " in the Hadoop binaries.");
  }
  return exeFile.getCanonicalPath();
}

/** a Windows utility to emulate Unix commands */
public static final String WINUTILS = getWinUtilsPath();

public static final String getWinUtilsPath() {
  String winUtilsPath = null;

  try {
    if (WINDOWS) {
      winUtilsPath = getQualifiedBinPath("winutils.exe");
    }
  } catch (IOException ioe) {
     LOG.error("Failed to locate the winutils binary in the hadoop binary path",
       ioe);
  }

  return winUtilsPath;
}


找到原因后就去网上问了度娘，找到了解决方案，很简单，如下：

1.下载winutils的windows版本

GitHub上，有人提供了winutils的windows的版本，项目地址是： https://github.com/srccodes/hadoop-common-2.2.0-bin ,直接下载此项目的zip包，下载后是文件名是hadoop-common-2.2.0-bin-master.zip,随便解压到一个目录 

2.配置环境变量

增加用户变量HADOOP_HOME，值是下载的zip包解压的目录，然后在系统变量path里增加$HADOOP_HOME\bin 即可。

再次运行程序，正常执行。

