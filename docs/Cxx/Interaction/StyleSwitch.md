[VTKExamples](/index/)/[Cxx](/Cxx)/Interaction/StyleSwitch

<img align="right" src="https://github.com/lorensen/VTKExamples/blob/gh-pages/Testing/Baseline/Interaction/TestStyleSwitch.png?raw=true" width="256" />

### Description
The class vtkInteractorStyleSwitch allows handles interactively switching between four interactor styles -- joystick actor, joystick camera, trackball actor, and trackball camera. Type 'j' or 't' to select joystick or trackball, and type 'c' or 'a' to select camera or actor. The default interactor style is joystick camera.

**StyleSwitch.cxx**
```c++
#include <vtkSmartPointer.h>
#include <vtkPolyDataMapper.h>
#include <vtkActor.h>
#include <vtkRenderWindow.h>
#include <vtkRenderer.h>
#include <vtkRenderWindowInteractor.h>
#include <vtkPolyData.h>
#include <vtkSphereSource.h>
#include <vtkInteractorStyleSwitch.h>

int main (int, char *[])
{

  vtkSmartPointer<vtkSphereSource> sphereSource = 
    vtkSmartPointer<vtkSphereSource>::New();
  
  // Create a mapper and actor
  vtkSmartPointer<vtkPolyDataMapper> mapper = 
    vtkSmartPointer<vtkPolyDataMapper>::New();
  mapper->SetInputConnection(sphereSource->GetOutputPort());

  vtkSmartPointer<vtkActor> actor = 
    vtkSmartPointer<vtkActor>::New();
  actor->SetMapper(mapper);

  // A renderer and render window
  vtkSmartPointer<vtkRenderer> renderer = 
    vtkSmartPointer<vtkRenderer>::New();
  vtkSmartPointer<vtkRenderWindow> renderWindow = 
    vtkSmartPointer<vtkRenderWindow>::New();
  renderWindow->AddRenderer(renderer);

  // An interactor
  vtkSmartPointer<vtkRenderWindowInteractor> renderWindowInteractor = 
      vtkSmartPointer<vtkRenderWindowInteractor>::New();
  renderWindowInteractor->SetRenderWindow(renderWindow);

  // Add the actors to the scene
  renderer->AddActor(actor);
  renderer->SetBackground(1,1,1); // Background color white

  // Render
  renderWindow->Render();

  vtkSmartPointer<vtkInteractorStyleSwitch> style = 
      vtkSmartPointer<vtkInteractorStyleSwitch>::New();
  
  renderWindowInteractor->SetInteractorStyle( style );
  
  // Begin mouse interaction
  renderWindowInteractor->Start();
  
  return EXIT_SUCCESS;
}
```
**CMakeLists.txt**
```cmake
cmake_minimum_required(VERSION 2.8)
 
PROJECT(StyleSwitch)
 
find_package(VTK REQUIRED)
include(${VTK_USE_FILE})
 
add_executable(StyleSwitch MACOSX_BUNDLE StyleSwitch.cxx)
 
target_link_libraries(StyleSwitch ${VTK_LIBRARIES})
```

**Download and Build StyleSwitch**

Click [here to download StyleSwitch](https://github.com/lorensen/VTKWikiExamplesTarballs/raw/master/StyleSwitch.tar) and its *CMakeLists.txt* file.
Once the *tarball StyleSwitch.tar* has been downloaded and extracted,
```
cd StyleSwitch/build 
```
If VTK is installed:
```
cmake ..
```
If VTK is not installed but compiled on your system, you will need to specify the path to your VTK build:
```
cmake -DVTK_DIR:PATH=/home/me/vtk_build ..
```
Build the project:
```
make
```
and run it:
```
./StyleSwitch
```
**WINDOWS USERS PLEASE NOTE:** Be sure to add the VTK bin directory to your path. This will resolve the VTK dll's at run time.
