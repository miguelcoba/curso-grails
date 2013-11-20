!SLIDE title-slide

# Grails Advanced #

!SLIDE subsection

# Advanced #

!SLIDE subsection

# Excel #

!SLIDE bullets

# Options #

* Apache POI
* Grails export plugin
* Grails excel-import plugin

!SLIDE small

# Excel with Apache POI #

## Source ##

    http://www.michaelvanvliet.nl/node/4

## Get POI ##

    - http://www.apache.org/dyn/closer.cgi/poi/release/
    - unzip package
    - put *.jar in tutorial/lib
    - grails create-controller excelPOI

!SLIDE code smaller

    package tutorial

    import org.apache.poi.hssf.model.Workbook;
    import org.apache.poi.hssf.usermodel.HSSFWorkbook;
    import org.apache.poi.hslf.model.Sheet
    import org.apache.poi.hssf.record.formula.functions.Row

    class ExcelPOIController {

      def index = {

        def nameOfWorkbook = 'workbook.xls'
        def locationToStoreWorkbook = '/tmp/' // Or C:\temp
        def wb = new HSSFWorkbook()
        def sheet = wb.createSheet("Demo")
        def row = sheet.createRow((short)0)
        row.createCell(1).setCellValue('Column 1')
        row.createCell(2).setCellValue('Column 2')
        row = sheet.createRow((short)1);
        row.createCell(0).setCellValue('Row 1')
        row.createCell(1).setCellValue('Hello')
        row.createCell(2).setCellValue('World')
        def fileWorkbook = new FileOutputStream(locationToStoreWorkbook + 
            nameOfWorkbook)
        wb.write(fileWorkbook)
        fileWorkbook.close()
        render('Workbook was saved to: ' + locationToStoreWorkbook + 
            nameOfWorkbook)
      }
    }

!SLIDE subsection

# Web Services #

!SLIDE bullets

# Options #

* REST: Grails only
* SOAP: Grails xfire plugin
* SOAP: Grails springws plugin

!SLIDE smaller

# SOAP with XFire plugin #

## Source ##

    http://groovy.codehaus.org/Using+the+Grails+XFire+plugin+and+GroovyWS

## Install XFire plugin ##

    $ grails install-plugin xfire
    $ grails create-service test

!SLIDE smaller

# Test Service #

    @@@ ruby
    package tutorial

    class TestService {

        boolean transactional = false

        static expose=['xfire']

        static conversions = [
          'AUD': [ 'USD': 100.00D, 'GBP': 44.44D ],
          'USD': [ 'AUD': 1.00D, 'GBP': 88.88D ],
          'GBP': [ 'AUD': 22.22D, 'USD': 33.33D ]
        ]

        Double convert(String from, String to, Double amount) {
            conversions[from][to] * amount
        }
    }

!SLIDE 

# WSDL #

    http://localhost:8080/tutorial/services/test?wsdl


!SLIDE code

# Web Service consumer #

## client.groovy ##
    
    @@@ ruby
import groovyx.net.ws.WSClient

@Grab(group='org.codehaus.groovy.modules', module='groovyws', version='0.5.2')
def getProxy(wsdl, classLoader) {
  new WSClient(wsdl, classLoader)
}
proxy = getProxy("http://localhost:8080/tutorial/services/test?wsdl", this.class.classLoader)
proxy.initialize()

result = proxy.convert("AUD", "USD", 10.0)
println "10 AUD are worth ${result} USD"

!SLIDE commandline incremental

# Consuming the service #

    $ groovy client.groovy
    10 AUD are worth 1000.0 USD

!SLIDE subsection

# Jasper #

!SLIDE bullets

# Options #

* Grails jasper plugin
* Grails dynamic-jasper plugin

!SLIDE small

# Reports with dynamic-jasper plugin #

## Source ##

    http://www.grails.org/plugin/dynamic-jasper

## Install dynamic-jasper plugin ##

    $ grails install-plugin dynamic-jasper
    $ grails create-domain-class User
    $ grails create-controller user

!SLIDE code smaller

# User controller class #
  
    @@@ ruby      
    package tutorial

    class UserController {

      def scaffold = true
    }

!SLIDE code smaller

# User domain class #

    @@@ ruby
    package tutorial

    class User {
      String username
      String name
      String lastName
      Boolean active

      static reportable = [:]

        static constraints = {
        }
    }

!SLIDE small

# View a report #

    http://localhost:8080/tutorial/djReport/index?entity=user

!SLIDE subsection

# jQuery #

!SLIDE bullets

# Options #

* Thousands of jQuery libraries
* jQuery UI
* jQuery Tools

!SLIDE smaller

# jQuery Tools #

## Source ##

    http://flowplayer.org/tools/download/index.html

## Install jQuery Tools ##

    <script src="http://cdn.jquerytools.org/1.2.5/full/jquery.tools.min.js">
    </script>

!SLIDE code smaller

# grails-app/view/book/jquery.gsp #

    <html>
      <head>
        <script src="http://cdn.jquerytools.org/1.2.5/full/jquery.tools.min.js">
        </script>
      </head>
      <body>
      </body>
    </html>

!SLIDE

# Book Controller #

    @@@ ruby
    class BookController {
      def jquery = {}
    }

!SLIDE code smaller

# Date Input #

    @@@ html
    <!-- dateinput styling -->
    <link rel="stylesheet" 
          type="text/css" 
          href="http://static.flowplayer.org/tools/
              demos/dateinput/css/skin1.css"/>
  
    <!-- HTML5 date input -->
    <input type="date" />
  
    <!-- make it happen -->
    <script>
      $(":date").dateinput();
    </script>


!SLIDE

# jQuery Tools #

    http://jqueryui.com/
    http://jqueryui.com/demo

    - Accordion
    - Autocomplete
    - Button
    - Dialog
    - Datepicker
    - Progressbar
    - Tabs
    - Slider

!SLIDE smaller

# Security #

## Spring Security Core Plugin ##

    http://www.grails.org/plugin/spring-security-core
    http://burtbeckwith.github.com/grails-spring-security-core/

    - Spring Security
    - DB
    - LDAP
    - OpenID
    - CAS

!SLIDE bullets

# Thanks #

* Miguel Cobá
* [miguel@trantaria.com](miguel@trantaria.com)

<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/"><img alt="Licencia de Creative Commons" style="border-width:0" src="http://i.creativecommons.org/l/by-nc-sa/3.0/88x31.png" /></a><br /><span xmlns:dct="http://purl.org/dc/terms/" href="http://purl.org/dc/dcmitype/Text" property="dct:title" rel="dct:type">Groovy Intro</span> by <a xmlns:cc="http://creativecommons.org/ns#" href="http://trantaria.com" property="cc:attributionName" rel="cc:attributionURL">Miguel Cobá</a> is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/">Creative Commons Attribution-NonCommercial-ShareAlike 3.0 Unported License</a>.
