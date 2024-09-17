# java-dynamic-compiler
Javaを動的コンパイルするライブラリ

## セットアップ方法
### mavenを使用した方法
pom.xmlに下記を記載
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">

  <repositories>
    <repository>
      <id>nakigao_rep</id>
      <url>https://raw.githubusercontent.com/2T-T2/nakigao-maven-repository/main/</url>
    </repository>
  </repositories>

  <dependencies>
    <dependency>
      <groupId>t_panda.compiler</groupId>
      <artifactId>t_panda.compiler.jar</artifactId>
      <version>0.0.0.0</version>
    </dependency>
  </dependencies>
</project>
```
### ソースをダウンロードしビルドする方法
ソースダウンロードする
```bat
curl -L -O "https://github.com/2T-T2/java-dynamic-compiler/archive/refs/heads/main.zip"
```
ダウンロードしたzip解凍後にprmj.batのあるフォルダに移動し下記実行
```bat
prjm.bat all
```
上記実行後に、同フォルダに <b>rep\t_panda\compiler\t_panda.compiler.jar\0.0.0.0\t_panda.compiler.jar-0.0.0.0.jar</b> が生成される。
<div><b><i>※pom.xml が存在しますが、mavenでのビルドは出来ません。。。</i></b></div>

### 使用例
```java
 TPCompiler compiler = new TPCompiler(ToolProvider.getSystemJavaCompiler());
 CompileTask task = compiler. createTask();
 final String pkgName    = "pkg.dynamic";
 final String className  = "Sample";
 final String fqcnSample = pkgName + "." + className;
 final String srcSample  = String.format(
     """
     package %s;

     public class %s implements java.util.Comparator<String> {
         @Override
         public int compare(String o1, String o2) {
             return o1.compareTo(o2);
         }
     }
     """, pkgName, className)
     ;

 compiler.getVersion().ifPresent(System.out::println);

 CompileResult result = task. addCompileTarget(fqcnSample , srcSample)  // ソース指定
     .addOption("-parameters")                                          // オプション追加
     .addOption("-g:source")
     .run();
 if( !result. isSuccess() ) {
     throw new Exception("コンパイルエラー\n" + result. getOutMessage() + "\n" + result. getErrMessage() );
 }

 Class<Comparator<String>> comparator = result.getCompiledClassAs(fqcnSample);
 List<String> strList = new ArrayList<>() {
     {
         add("bbb");
         add("aaa");
         add("ccc");
     }
 };
 strList.sort(comparator.getConstructor().newInstance());

 System.out.println(strList.toString());     // output [aaa, bbb, ccc]
```

### ドキュメント
<a href="http://www.example.com">http://www.example.com</a>

### 補足
```bat
prjm.bat help

prjm.bat clean
    .\lib\, .\out\, .\dst\, .\javadoc\ をクリーンします

prjm.bat dl-depend
    .\lib\ に pom.xml で指定された依存ファイルをダウンロードします

prjm.bat compile
    .\src\ の内容をコンパイルします
    出力先フォルダ .\out\

prjm.bat archive
    .\out\, .\src\, .\res\ をアーカイブ化してjarを作成します
    出力先ファイル名 .\dst\my.test.jar

prjm.bat mvnrep
    .\dst\my.test.jar, pom.xml からmavenリポジトリの作成を行います
    出力先ファイル名 .\dst\my.test.jar

prjm.bat javadoc
    ドキュメントを生成します
    出力先フォルダ .\javadoc\

prjm.bat all
    clean -> dl-depend -> compile
    -> archive -> mvnrep -> javadoc の順で実行します
```
