Overview
^^^^^^^^

The concept is simple a transformation from a string value to a concrete type.

The coercion framework has tooling to derive the type information as a **CoercionTarget**

Standard Coercions
^^^^^^^^^^^^^^^^^^
The standard coercions cover most of the standard java libraries

  | These examples all assume that the configured field is in a class called ``Bean``

String

  Obviously strings are one to one::
  
    @Configured
    String value;
    
    bean.value=Some String

StringConstructor

  Anything with a single string constructor is obviously coercable::
  
    @Configured
    URL url;
    
    bean.url=https://www.example.com/path
  
Enum

  Enums are mapped based on the name of the enum.
  
  I consider Enums to be classes and so camel case them e.g.::
  
    public enum Status {
      Good,
      Bad
    }
    
    @Configured
    Status value;
  
    bean.value=Good
  
Pattern

  For regular expressions, super handy with default values in case you missed some cases and need a quick update in production
  
  This example derives the names of the standard coercions::
  
    @Configured
    Pattern pattern = Pattern.compile(".*");
  
    bean.pattern=*/\(.*\)Coercion.java
    
ValueOfMethod

  Anything with a ``valueOf(String)`` or ``of(String)`` factory method can be coerced::
  
    @Configured
    Integer count = 20;
    
    bean.count=10
  
ParseMethod

  Anything with a ``parse(String)`` factory method can be coerced, this includes lots of the time classes::
  
    @Configured
    Duration length = Duration.ofSeconds(10);
    
    bean.length=PT20s

DateTimeFormatter

  For building time formatters
  
InetSocketAddress
  
  Creates inet sockets and validates that they are available
  
Class
  
  Loads a class from a fully qualifed name::
    
    type=com.example.ConcreteBean
    
CharacterSet

  Externalise a character set useful for file operations integration with disparate systems

Collection Coercions
^^^^^^^^^^^^^^^^^^^^

Collection coercions are nested in that the elements use other coercions to derive the components.

Map::

  some.map=a=b,c=d,e=f (1)
  alternateSeparator=[;]a=b,c;c=d;e=f (2)
  
  @Configured
  Map map;
  
Collection::

  some.collection=a,c,c,d,e
  another=[;]a;b;c;d;e (3)
  aThird=a,c,e,b,f
  
Array

  anArray=1,2,3,4,5
  
#. Maps are a list of key=value
#. alternate separators can be defined if you need to use the standard comma separator in the value
#. alternate separators work for all collections
#. obviously strings and numbers and anything coercable is supported

Adding Coercions
^^^^^^^^^^^^^^^^

Coercions are based around the Coercion interface::

  public interface Coercion<T> {
  
    boolean isApplicableTo(CoercionTarget target); 

    T coerce(CoercionTarget type, String value); 
  
    boolean hasDefaultValue(); 

    T getDefaultValue(CoercionTarget target);
    
    boolean isInverted();

  }

Most Coercions will extend ``AbstractNoDefaultCoercion`` that only leaves ``isApplicableTo`` and ``coerce`` for implementation
  
isApplicableTo

  This allows the coercion to decide if the target is something it can coerce
  
  Its a method not just a type as more complicate coercions required more information than just the type e.g. collections have nested potentially parameterized types
  
coerce

  The core of the coercion it takes a string value and returns a real value
  
  It should always fail for invalid values
  
  The value will never be null but can be blank
  
hasDefaultValue

  Some coercions can have a system default value
  
  CharacterSet is a good example that has the system defined character set
  
getDefaultValue

  Derive a default value from the target in the absense of a value
  
isInverted

  In some cases a coercion will return a fully fledged bean from the Dependency Injection container in which case there is no need for injection to happen to the configured value
  

Example coercion
^^^^^^^^^^^^^^^^

This is the Pattern Coercion::

  package net.stickycode.coercion;

  import java.util.regex.Pattern;
  import java.util.regex.PatternSyntaxException;

  import net.stickycode.stereotype.plugin.StickyExtension;

  @StickyExtension (1)
  public class PatternCoercion
      extends AbstractNoDefaultCoercion<Pattern> { (2)

    @Override
    public Pattern coerce(CoercionTarget type, String value) (3)
        throws PatternCouldNotBeCoercedException {
      try {
        return Pattern.compile(value);
      }
      catch (PatternSyntaxException e) {
        throw new PatternCouldNotBeCoercedException(e, value); (4)
      }
    }

    @Override
    public boolean isApplicableTo(CoercionTarget type) {
      return type.getType().isAssignableFrom(Pattern.class); (5)
    }

  }

#. The framework is made of components and a coercion is just a component and uses DI, the ``@StickyExtension`` stereotype allows framework components to be initialised first
#. Extending the abstract no default coercion to keep implementation simple and consistent
#. The standard method returns a new Pattern
#. I always use explicit exceptions as they are more meaningful that generic expections with odd messages
#. The coercion is only valid for Pattern types

