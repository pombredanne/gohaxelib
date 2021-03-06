gohaxelib
=========

This software helps [Go](http://golang.org) programmers using the [TARDIS Go->Haxe transpiler](http:tardisgo.github.io) to call Haxe libraries by providing automated definitions of those libraries in Go.

Go definitions of Haxe libraries currently available are:

- import "github.com/tardisgo/gohaxelib/_haxeapi"  // the api for the standard Haxe library http://api.haxe.org/
- import "github.com/tardisgo/gohaxelib/_openfl"   // the api for the OpenFL framework http://www.openfl.org/developer/documentation/api/

The leading underscore "_" in the library names signals to the transpiler that they are Haxe packages (but see "Status & Issues" section below).

Within the library, each Go name converted from Haxe has a leading capital letter to make it exported in Go and to signal in which Haxe execution environment the name can be used (please let the
[author](https://github.com/elliott5) know if there is an experimental target that has been missed):
-	C = (reserved for C see experimental https://github.com/waneck/haxe-genc/)
- 	D = (reserved for Dart see experimental https://bitbucket.org/AndrewVernon/hx2dart ) 
-	F = Flash (v9) 
-	H = PHP 
-	J = Java 
-	N = Neko 
-	P = C++ 
- 	R = C# 
-	S = JavaScript  
-	X = cross-platform
-	Y = (reserved for Python see experimental https://github.com/frabbit/hx2python )

Dots "." in Haxe names are converted to underscore in Go, and underscore to triple underscore. Haxe class fields are used through accessor functions with the suffix underscore “goset" and underscore "goget". So for example Haxe class field ["haxe.Serializer.USE_CACHE"] (http://api.haxe.org/haxe/Serializer.html#USE_CACHE) generates two entries:
- func Xhaxe_Serializer_USE___CACHE_goget()  bool { return false; }
- func Xhaxe_Serializer_USE___CACHE_goset(bool){}

The Go type "uintptr" maps to Haxe type "Dynamic", it is used extensively where a suitable Go type does not exist. 

Definitions of the various APIs are generated using the -xml flag on the Haxe compiler as described on this page http://haxe.org/doc/haxedoc. 
For the standard library, the XML files were generated using the instructions at the bottom of this page http://haxe.org/manual/documentation. 
The main haxelib2go.hx program is derived from the example in the centre of that same page.

Go code is generated by traversing the CType descriptions of Haxe library code unpacked from the xml files.
See the CType api documentation at http://api.haxe.org/haxe/rtti/CType.html . Unfortunately there does not appear to be any higher level descriptions of this datastructure, therefore much of this software's development has been a matter of trial and error.

To run the program to generate the library definitions (in the simplest but slowest way), from the top level directory run $ haxe -main Haxelib2go --interp

Obviously you will need to have the Haxe 3.0.1+ compiler installed to do this, see http://haxe.org/download .

Status & Issues
---------------

This is experimental code that is certain to change, so it is probably not even at version 0.0.1 yet. 

Long term, the functionality in this code needs to be repackaged into a user-oriented tool that can generate the Go definitions from a given XML Haxe definition file.

The known issues are:
- Definitions that should be generated from "extends" and/or "implements" declarations are not generated yet, so you must do an explict cast in Go to the required type in order to access this "super-class" functionality.
- There is no command line interface, everything is hard coded, so there is no way to create new library definitions without changing the code.
- There is no auto-update process to follow api updates.
- There is no interface version tracking.
- Each library name currently has a leading underscore "_" to signal to the TARDIS Go transpiler that it is a Haxe library, so that it's Go code is replaced by the correct Haxe code post-translation, but this requirement may change as the main transpiler software evolves. 
- Go code contains stub functions so that it will compile using the existing compilers, this should allow Go native and TARDIS Go transpiler code to co-exist, provided the functions never get called by the native code. This may not be the best route long term, experimentation may be required because this issue relates to the very different ways that Haxe and Go handle target-specific conditional compilation. For the moment, stub functions seem least likely to cause problems. 
- Haxe structures with field names are not currently used by the TARDIS Go implementation (it treats structures as arrays), extra coding may be required to allow them to be accessed from Go along the lines of goset() and goget().
- The licensing declarations may need to be improved for included code, the   [author](https://github.com/elliott5) is not sure of the exact rules.

But there will also be many unknown issues... 

 
Contributing
------------

The  [author](https://github.com/elliott5) would be delighted if others want to contribute to this project, but please consider talking to him about what you are planning to do first, or discussing it in the [tardisgo](https://groups.google.com/d/forum/tardisgo) Google group, so that we don't get any duplicated effort.
