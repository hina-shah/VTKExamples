[VTKExamples](Home)/[Cxx](Cxx)/Lighting/Light

<img align="right" src="https://github.com/lorensen/VTKExamples/raw/master/Testing/Baseline/Lighting/TestLight.png" width="256" />

**Light.cxx**
```c++
#include <cstdlib>

#include <vtkVersion.h>
#include <vtkLightCollection.h>
#include <vtkProperty.h>
#include <vtkRenderer.h>
#include <vtkRenderWindow.h>
#include <vtkPlaneSource.h>
#include <vtkSphereSource.h>
#include <vtkPolyDataMapper.h>
#include <vtkActor.h>
#include <vtkLight.h>
#include <vtkLightActor.h>
#include <vtkSmartPointer.h>
#include <vtkRenderWindowInteractor.h>

int main(int, char *[])
{
  vtkSmartPointer<vtkRenderer> renderer = vtkSmartPointer<vtkRenderer>::New();

  double lightPosition[3] = {0, 0, 1};

  // Create a light
  double lightFocalPoint[3] = {0,0,0};

  vtkSmartPointer<vtkLight> light = vtkSmartPointer<vtkLight>::New();
  light->SetLightTypeToSceneLight();
  light->SetPosition(lightPosition[0], lightPosition[1], lightPosition[2]);
  light->SetPositional(true); // required for vtkLightActor below
  light->SetConeAngle(10);
  light->SetFocalPoint(lightFocalPoint[0], lightFocalPoint[1], lightFocalPoint[2]);
  light->SetDiffuseColor(1,0,0);
  light->SetAmbientColor(0,1,0);
  light->SetSpecularColor(0,0,1);

  vtkLightCollection* originalLights = renderer->GetLights();
  std::cout << "Originally there are " << originalLights->GetNumberOfItems() << " lights." << std::endl;

//  renderer->AddLight(light); // can't do this here - must do this after the renderWindow->Render() below

  // Display where the light is
  vtkSmartPointer<vtkLightActor> lightActor = vtkSmartPointer<vtkLightActor>::New();
  lightActor->SetLight(light);
  renderer->AddViewProp(lightActor);

  // Display where the light is focused
  vtkSmartPointer<vtkSphereSource> lightFocalPointSphere = vtkSmartPointer<vtkSphereSource>::New();
  lightFocalPointSphere->SetCenter(lightFocalPoint);
  lightFocalPointSphere->SetRadius(.1);
  lightFocalPointSphere->Update();

  vtkSmartPointer<vtkPolyDataMapper> lightFocalPointMapper =
      vtkSmartPointer<vtkPolyDataMapper>::New();
  lightFocalPointMapper->SetInputConnection(lightFocalPointSphere->GetOutputPort());

  vtkSmartPointer<vtkActor> lightFocalPointActor = vtkSmartPointer<vtkActor>::New();
  lightFocalPointActor->SetMapper(lightFocalPointMapper);
  lightFocalPointActor->GetProperty()->SetColor(1.0, 1.0, 0.0); //(R,G,B)
  renderer->AddViewProp(lightFocalPointActor);

  // Create a plane for the light to shine on
  vtkSmartPointer<vtkPlaneSource> planeSource = vtkSmartPointer<vtkPlaneSource>::New();
  planeSource->SetResolution(100, 100);
  planeSource->Update();

  vtkSmartPointer<vtkPolyDataMapper> planeMapper = vtkSmartPointer<vtkPolyDataMapper>::New();
#if VTK_MAJOR_VERSION <= 5
  planeMapper->SetInput(planeSource->GetOutput());
#else
  planeMapper->SetInputData(planeSource->GetOutput());
#endif

  vtkSmartPointer<vtkActor> planeActor = vtkSmartPointer<vtkActor>::New();
  planeActor->SetMapper(planeMapper);
  renderer->AddActor(planeActor);

  vtkSmartPointer<vtkRenderWindow> renderWindow = vtkSmartPointer<vtkRenderWindow>::New();
  renderWindow->AddRenderer(renderer);

  vtkSmartPointer<vtkRenderWindowInteractor> renderWindowInteractor =
      vtkSmartPointer<vtkRenderWindowInteractor>::New();
  renderWindowInteractor->SetRenderWindow(renderWindow);

  renderWindow->Render();
  std::cout << "Now there are " << originalLights->GetNumberOfItems() << " lights." << std::endl;


  renderer->AddLight(light); // must do this after renderWindow->Render();
  std::cout << "Now there are " << originalLights->GetNumberOfItems() << " lights." << std::endl;


  renderWindowInteractor->Start();

  return EXIT_SUCCESS;
}
```
**CMakeLists.txt**
```cmake
cmake_minimum_required(VERSION 2.8)
 
PROJECT(Light)
 
find_package(VTK REQUIRED)
include(${VTK_USE_FILE})
 
add_executable(Light MACOSX_BUNDLE Light.cxx)
 
target_link_libraries(Light ${VTK_LIBRARIES})
```

**Download and Build Light**

Click [here to download Light](https://github.com/lorensen/VTKWikiExamplesTarballs/raw/master/Light.tar) and its *CMakeLists.txt* file.
Once the *tarball Light.tar* has been downloaded and extracted,
```
cd Light/build 
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
./Light
```
**WINDOWS USERS PLEASE NOTE:** Be sure to add the VTK bin directory to your path. This will resolve the VTK dll's at run time.
