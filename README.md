**Introduction**

This is the project used to show the mechanism of gmsh.
It can be regarded as a guide to read the source code of gmsh.

**How a geo file is loaded into gmsh**

main() 
&emsp;-> GmshMainBatch()   <br/>
&emsp;&emsp;-> GmshBatch()   <br/>
&emsp;&emsp;&emsp;-> OpenProject() which will load the input file  <br/>
&emsp;&emsp;&emsp;&emsp;-> GModel::readGEO()  <br/>
&emsp;&emsp;&emsp;&emsp;&emsp;-> ParseFile()  <br/>
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;-> gmsh_yyparse() which is implemented at Gmsh.tab.cpp:6741 and it will parse the file and add the following internal components for the current model  <br/>
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;-> GModel::current() -> GModel::getGEOInternals() -> GEO_Internals::addVertex()  &emsp;
GEO_Internals::addLine() &emsp;
GEO_Internals::addSpline() &emsp;
GEO_Internals::addCompoundSpline() &emsp;
GEO_Internals::addCompoundBSpline() &emsp;
GEO_Internals::addCircleArc() &emsp;
GEO_Internals::addEllipseArc() &emsp;
GEO_Internals::addEllipseArc() &emsp;
GEO_Internals::addBSpline()&emsp;
GEO_Internals::addBezier() &emsp;
GEO_Internals::addBSpline() &emsp;
GEO_Internals::addCurveLoop() &emsp;
GEO_Internals::addPlaneSurface() &emsp;
GEO_Internals::addSurfaceFilling() &emsp;
GEO_Internals::addSurfaceFilling() &emsp;
GEO_Internals::addSurfaceLoop() &emsp;
GEO_Internals::addVolume() &emsp;
GEO_Internals::addDiscreteSurface(); &emsp; <br/>
&emsp;&emsp;&emsp;&emsp;&emsp; ->  GModel::current() -> GModel::getGEOInternals() -> GEO_Internals::synchronize() this function will synchronize the elements in GEO_Internals with the entities in GModel (from GEO types to gmsh types) <br/>
&emsp;&emsp;&emsp;->GModel::current()->mesh() this function will mesh the model