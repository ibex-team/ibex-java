
 # Java Plugin (for Choco)

<table>
<tr>
  <td><image src="http://ibex-team.github.io/ibex-lib/_images/ibex.jpg" style="height:150px" /></td>
  <td><image src="http://ibex-team.github.io/ibex-lib/_images/choco.png" style="height:150px" /></td>
</tr>
</table>
  
The Java plugin of Ibex allows to use Ibex with [Choco](https://choco-solver.org/), for solving mixed integer-continuous CSP
(constraint satisfaction problems).

> [!IMPORTANT]
> Starting release 2.9 of Ibex, the java interface will **no longer be a separate plugin** but integrated in the kernel.
> You just have to run `cmake` with `-DBUILD_JAVA_INTERFACE=ON` option.
> The installation procedure below is only valid for release before 2.8.

 The installation of the plugin will generate, in addition to the Ibex library, the libibex-java library that contains the glue code between C++ and Java.

> [!NOTE]
> Under Windows, Ibex is compiled as a 32-bit library although the platform is 64 bits (this is mainly because the MinGW environment is 32-bits). Hence, Java will fail in loading Ibex unless you have a 32-bits JVM.
> The following instructions must be typed in the shell of MinGW.

## Linux/MacOS
- Install ibex following instructions [here ](http://ibex-team.github.io/ibex-lib/install.html)
- Uncompress the archive ibex-2.8.9.tar.gz in some Ibex folder::
	`~/Ibex/$ tar xvfz ibex-2.8.9.tar.gz` 
- Download the last release of the ibex-java plugin (1.0.0)
- Uncompress the archive `ibex-java-1.0.0.tar.gz`
- Rename the folder `ibex-java-1.0.0` to `ibex-java` and place this folder under `~/Ibex/ibex-2.8.9/plugins`
- Set the environment variable ``JAVA_HOME``. Typical paths are ``/Library/Java/Home`` (MacOS) or ``/usr/lib/jvm/java-7-openjdk-i38`` (Linux). Example::
  ``~/Ibex/$ export JAVA_HOME=/Library/Java/Home``

- Then configure and install Ibex as follows::
  ```
  ~/Ibex/$ cd ibex-2.8.9
  ~/Ibex/ibex-2.8.9/$ ./waf configure [...] --enable-shared --with-jni --java-package-name=org.chocosolver.solver.constraints.real
  ~/Ibex/ibex-2.8.9/$ ./waf install
	```
  **Note**: the ``--enable-shared`` option is mandatory. 

# Windows
- Install ibex following instructions [here ](http://ibex-team.github.io/ibex-lib/install.html)
- Open a MinGW prompt window. 
- Uncompress the archive ibex-2.8.9.tar.gz in some Ibex folder::
	``~/Ibex/$ tar xvfz ibex-2.8.9.tar.gz``
- Download the last release of the ibex-java plugin (1.0.0)
- Uncompress the archive ``ibex-java-1.0.0.tar.gz``
- Rename the folder ``ibex-java-1.0.0`` to ``ibex-java`` and place this folder under ``~/Ibex/ibex-2.8.9/plugins``
- Set the environment variable ``JAVA_HOME`` to the home directory of the 32 bits JDK. The variable must be set in Linux-style (don't use backslash ("\\") as separator), e.g.::
  ```
	~/Ibex/$ export JAVA_HOME=/c/Java/jdk1.7.1_17
  ```
- Update the path as follows::
  ``~/Ibex/$ set PATH=%PATH%;[prefix]\lib;C:\MinGW\bin``
  where *[prefix]* is the path specified via ``--prefix``
	
  **Warning**: The path must not contain white spaces, like ”/c/Program Files/...”. Create a symbolik link of your Java directory if necessary.

- Then configure Ibex as follows::

  ```
	~/Ibex/$ cd ibex-2.8.9
	~/Ibex/ibex-2.8.9/$ ./waf configure [...] --enable-shared --with-jni --java-package-name=org.chocosolver.solver.constraints.real
	~/Ibex/ibex-2.8.9/$ ./waf install
  ```
  
  **Note**: the ``--enable-shared`` option is mandatory. 

## Configuration options

The IbexOpt plugin supports the following options (to be used with `waf configure`) :

| Option | Description |
| --- | --- |
|`--with-jni`| Activate the Java plugin. |
|`--java-package-name=PACKAGE_NAME`| The plugin will create the PACKAGE_NAME.jar file. This file is put into the ``[prefix]/share/java`` where [prefix] is ``/usr/local`` by default or whatever path specified via ``--prefix``.|

## Troubleshooting

### UnsatisfiedLinkError with Choco

When running the “CycloHexan” example from Choco using Ibex, the following error appears::

	Exception in thread "main" java.lang.UnsatisfiedLinkError: org.chocosolver.solver.constraints.real.Ibex.add_ctr(ILjava/lang/String;I)V
	at org.chocosolver.solver.constraints.real.Ibex.add_ctr(Native Method)
	at org.chocosolver.solver.constraint.propagators.real.RealPropagator.&lt;init&gt;(RealPropagator.java:77)
	at org.chocosolver.solver.constraints.real.RealConstraint.addFunction(RealConstraint.java:82)
	at samples.real.CycloHexan.buildModel(CycloHexan.java:87)
	at samples.AbstractProblem.execute(AbstractProblem.java:130)
	at samples.real.CycloHexan.main(CycloHexan.java:134)

**Solution**: You probably did not set the Java package properly. The java package of the Ibex class in Choco is org.chocosolver.solver.constraints.real, try::

	./waf configure [....] --java-package-name=org.chocosolver.solver.constraints.real

### JAVA_HOME does not seem to be set properly

I get this message when running waf configure.

**Solution**: The ``JAVA_HOME`` must be the path of the JDK and contain a subdirectoy include which, in turn, contains the jni.h header file (for Java versions <10). 
On MacOS this path can be ``/Library/Java/JavaVirtualMachines/jdkXXXX.jdk/Contents/Home``.
