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
