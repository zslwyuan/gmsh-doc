**Introduction**

This is the project used to show the mechanism of gmsh.
It can be regarded as a guide to read the source code of gmsh.

**The Call Flow for A Meshing Procedure**

main() 
    -> GmshMainBatch() 
        -> GmshBatch() 
            -> OpenProject() which will load the input file
               -> readGEO()
                  -> ParseFile()
                     -> gmsh_yyparse() which is implemented at Gmsh.tab.cpp:6741