The Servlet interface is the central abstraction of the Java Servlet API. All servlets
implement this interface either directly, or more commonly, by extending a class that
implements the interface. The two classes in the Java Servlet API that implement the
Servlet interface are GenericServlet and HttpServlet. For most purposes, Developers
will extend HttpServlet to implement their servlets.

The handling of concurrent requests to a Web application generally requires
that the Web Developer design servlets that can deal with multiple threads executing
within the service method at a particular time.
Generally the Web container handles concurrent requests to the same servlet
by concurrent execution of the service method on different threads. 


For a servlet not hosted in a distributed environment (the default), the servlet
container must use only one instance per servlet declaration. However, for a servlet
implementing the SingleThreadModel interface, the servlet container may
instantiate multiple instances to handle a heavy request load and serialize requests
to a particular instance

After the servlet object is instantiated, the container must initialize the servlet before
it can handle requests from clients. Initialization is provided so that a servlet can
read persistent configuration data, initialize costly resources (such as JDBC™ APIbased
connections), and perform other one-time activities. The container initializes
the servlet instance by calling the init method of the Servlet interface with a
unique (per servlet declaration) object implementing the ServletConfig interface.
This configuration object allows the servlet to access name-value initialization
parameters from the Web application’s configuration information. The configuration
object also gives the servlet access to an object (implementing the ServletContext
interface) that describes the servlet’s runtime environment. See Chapter SRV.4,
“Servlet Context” for more information about the ServletContext interface.

Path parameters that are part of a GET request (as defined by HTTP 1.1) are not
exposed by these APIs. They must be parsed from the String values returned by the
getRequestURI method or the getPathInfo method.

Context Path: The path prefix associated with the ServletContext that this
servlet is a part of. If this context is the “default” context rooted at the base of
the Web server’s URL name space, this path will be an empty string. Otherwise,
if the context is not rooted at the root of the server’s name space, the path
starts with a’/’ character but does not end with a’/’ character.
• Servlet Path: The path section that directly corresponds to the mapping
which activated this request. This path starts with a’/’ character except in the
case where the request is matched with the ‘/*’ pattern, in which case it is an
empty string.
• PathInfo: The part of the request path that is not part of the Context Path or
the Servlet Path. It is either null if there is no extra path, or is a string with a
leading ‘/’


## Servlet Context
The ServletContext interface defines a servlet’s view of the Web application
within which the servlet is running. The Container Provider is responsible for
providing an implementation of the ServletContext interface in the servlet
container. Using the ServletContext object, a servlet can log events, obtain URL
references to resources, and set and store attributes that other servlets in the context
can access.

The following methods of the ServletContext interface allow the servlet access to
context initialization parameters associated with a Web application as specified by
the Application Developer in the deployment descriptor:
• getInitParameter
• getInitParameterNames

A servlet can bind an object attribute into the context by name. Any attribute bound
into a context is available to any other servlet that is part of the same Web
application. The following methods of ServletContext interface allow access to
this functionality:
• setAttribute
• getAttribute
• getAttributeNames
• removeAttribute

The ServletContext interface provides direct access only to the hierarchy of static
content documents that are part of the Web application, including HTML, GIF, and
JPEG files, via the following methods of the ServletContext interface:
• getResource
• getResourceAsStream

##Filters
A filter is a reusable piece of code that can transform the content of HTTP requests,
responses, and header information. Filters do not generally create a response or
respond to a request as servlets do, rather they modify or adapt the requests for a
resource, and modify or adapt responses from a resource. 

A filter is declared using the <filter>
element in the deployment descriptor. A filter or collection of filters can be
configured for invocation by defining <filter-mapping> elements in the
deployment descriptor. This is done by mapping filters to a particular servlet by
the servlet’s logical name, or mapping to a group of servlets and static content
resources by mapping a filter to a URL pattern

Only one instance per <filter> declaration in the deployment descriptor is
instantiated per JVM of the container.

##sessions
SRV.7.7.1 Threading Issues
Multiple servlets executing request threads may have active access to a single
session object at the same time. Access to the session object should be
synchronized, however, the Developer has the responsibility for synchronizing
access to session resources as appropriate.

##request dispatcher
When building a Web application, it is often useful to forward processing of a
request to another servlet, or to include the output of another servlet in the response.
The RequestDispatcher interface provides a mechanism to accomplish this.

##Web Applications
A special directory exists within the application hierarchy named “WEB-INF”.
This directory contains all things related to the application that aren’t in the
document root of the application. The WEB-INF node is not part of the public
document tree of the application. No file contained in the WEB-INF directory may
be served directly to a client by the container. However, the contents of the WEBINF
directory are visible to servlet code using the getResource and getResourceAsStream
method calls on the ServletContext, and may be exposed using the
RequestDispatcher calls

The contents of the WEB-INF directory are:
• The /WEB-INF/web.xml deployment descriptor.
• The /WEB-INF/classes/ directory for servlet and utility classes. The classes in
this directory must be available to the application class loader.
• The /WEB-INF/lib/*.jar area for Java ARchive files. These files contain servlets,
beans, and other utility classes useful to the Web application. The Web application
class loader must be able to load classes from any of these archive
files.
The Web application class loader must load classes from the WEB-INF/ classes
directory first, and then from library JARs in the WEB-INF/lib directory.

##Deployment Descriptor

