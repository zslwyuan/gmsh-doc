**Introduction**

This is the project used to show the mechanism of gmsh.
It can be regarded as a guide to read the source code of gmsh.

I tend to use Doxygen to visualize the overall interaction between functions (just like the BFS algorithm) and use GDB to trace the call stack of the functions (just like the DFS algorithm).

**How a geo file is loaded into gmsh**

main()   <br/>
&emsp;-> GmshMainBatch()   <br/>
&emsp;&emsp;-> GmshBatch()   <br/>
&emsp;&emsp;-> GmshInitialize() which resets all the parameters to defaul values<br/>
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

**How a GModel get meshed**

main()  <br/>
&emsp;-> GmshMainBatch()   <br/>
&emsp;&emsp;-> GmshBatch()   <br/>
&emsp;&emsp;&emsp;-> GModel::mesh()   <br/>
&emsp;&emsp;&emsp;&emsp;-> GenerateMesh()   <br/>
&emsp;&emsp;&emsp;&emsp;&emsp;-> SetOrder1()  this function reset high-order elements to elements with the order of 1<br/>
&emsp;&emsp;&emsp;&emsp;&emsp;-> FixPeriodicMesh() don't know its usage temporarily<br/>
&emsp;&emsp;&emsp;&emsp;&emsp;-> Mesh0D() which initializes the mesh vertexes for later processing  <br/>
&emsp;&emsp;&emsp;&emsp;&emsp;-> Mesh1D() which mainly meshes the GEdge  <br/>
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;-> GEdge::mesh() which mainly meshes the GEdges  <br/>
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;-> meshGEdge::operator()()  <br/>
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;-> meshGEdgeProcessing()  <br/>
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;-> Integration() and RecursiveIntegration() which will utilize bi-section approaches to mesh GEdge based on given constraints, such as mesh size (!! "lc" in the code means characteristic length or mesh size ), and the integration can be regarded as side products. <br/>
&emsp;&emsp;&emsp;&emsp;&emsp;-> Mesh2D() which mainly meshes the GFace  <br/>
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;-> GFace::mesh() which mainly meshes the GFaces  <br/>
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;-> meshGFace::operator()() which can define the mesh algorithm (default: Frontal-Delaunay) <br/>
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;-> meshGenerator()  <br/>
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;->GFaceInitialMesh() which splits a face into delaunay triangles  <br/>
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;->algoDelaunay2D() which splits a face into delaunay triangles  <br/>