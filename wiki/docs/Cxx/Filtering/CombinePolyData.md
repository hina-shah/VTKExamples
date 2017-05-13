[VTKExamples](Home)/[Cxx](Cxx)/Filtering/CombinePolyData

<img align="right" src="https://github.com/lorensen/VTKExamples/raw/master/Testing/Baseline/Filtering/TestCombinePolyData.png" width="256" />

### Description
This example reads two .vtp files (or produces them if not specified as command line arguments), combines them, and displays the result to the screen.

**CombinePolyData.cxx**
```c++
#include <vtkVersion.h>
#include <vtkSmartPointer.h>
#include <vtkPolyData.h>
#include <vtkSphereSource.h>
#include <vtkConeSource.h>
#include <vtkXMLPolyDataReader.h>
#include <vtkCleanPolyData.h>
#include <vtkAppendPolyData.h>
#include <vtkPolyDataMapper.h>
#include <vtkActor.h>
#include <vtkRenderWindow.h>
#include <vtkRenderer.h>
#include <vtkRenderWindowInteractor.h>

int main(int argc, char *argv[])
{
  vtkSmartPointer<vtkPolyData> input1 =
    vtkSmartPointer<vtkPolyData>::New();
  vtkSmartPointer<vtkPolyData> input2 =
    vtkSmartPointer<vtkPolyData>::New();

  if(argc == 1) //command line arguments not specified
  {
    vtkSmartPointer<vtkSphereSource> sphereSource =
      vtkSmartPointer<vtkSphereSource>::New();
    sphereSource->SetCenter(5,0,0);
    sphereSource->Update();

    input1->ShallowCopy(sphereSource->GetOutput());

    vtkSmartPointer<vtkConeSource> coneSource =
      vtkSmartPointer<vtkConeSource>::New();
    coneSource->Update();

    input2->ShallowCopy(coneSource->GetOutput());
  }
  else
  {
    if(argc != 3)
    {
      std::cout << "argc = " << argc << std::endl;
      std::cout << "Required arguments: File1 File2" << std::endl;
      return EXIT_FAILURE;
    }
    std::string inputFilename1 = argv[1];
    std::string inputFilename2 = argv[2];
    vtkSmartPointer<vtkXMLPolyDataReader> reader1 =
      vtkSmartPointer<vtkXMLPolyDataReader>::New();
    reader1->SetFileName(inputFilename1.c_str());
    reader1->Update();
    input1->ShallowCopy(reader1->GetOutput());

    vtkSmartPointer<vtkXMLPolyDataReader> reader2 =
      vtkSmartPointer<vtkXMLPolyDataReader>::New();
    reader2->SetFileName(inputFilename2.c_str());
    reader2->Update();
    input2->ShallowCopy(reader2->GetOutput());
  }

  //Append the two meshes
  vtkSmartPointer<vtkAppendPolyData> appendFilter =
    vtkSmartPointer<vtkAppendPolyData>::New();
#if VTK_MAJOR_VERSION <= 5
  appendFilter->AddInputConnection(input1->GetProducerPort());
  appendFilter->AddInputConnection(input2->GetProducerPort());
#else
  appendFilter->AddInputData(input1);
  appendFilter->AddInputData(input2);
#endif
  appendFilter->Update();

  // Remove any duplicate points.
  vtkSmartPointer<vtkCleanPolyData> cleanFilter =
    vtkSmartPointer<vtkCleanPolyData>::New();
  cleanFilter->SetInputConnection(appendFilter->GetOutputPort());
  cleanFilter->Update();

  //Create a mapper and actor
  vtkSmartPointer<vtkPolyDataMapper> mapper =
    vtkSmartPointer<vtkPolyDataMapper>::New();
  mapper->SetInputConnection(cleanFilter->GetOutputPort());

  vtkSmartPointer<vtkActor> actor =
    vtkSmartPointer<vtkActor>::New();
  actor->SetMapper(mapper);

  //Create a renderer, render window, and interactor
  vtkSmartPointer<vtkRenderer> renderer =
    vtkSmartPointer<vtkRenderer>::New();
  vtkSmartPointer<vtkRenderWindow> renderWindow =
    vtkSmartPointer<vtkRenderWindow>::New();
  renderWindow->AddRenderer(renderer);
  vtkSmartPointer<vtkRenderWindowInteractor> renderWindowInteractor =
    vtkSmartPointer<vtkRenderWindowInteractor>::New();
  renderWindowInteractor->SetRenderWindow(renderWindow);

  //Add the actors to the scene
  renderer->AddActor(actor);
  renderer->SetBackground(.3, .2, .1); // Background color dark red

  //Render and interact
  renderWindow->Render();
  renderWindowInteractor->Start();

  return EXIT_SUCCESS;
}
```
**CMakeLists.txt**
```cmake
cmake_minimum_required(VERSION 2.8)
 
PROJECT(CombinePolyData)
 
find_package(VTK REQUIRED)
include(${VTK_USE_FILE})
 
add_executable(CombinePolyData MACOSX_BUNDLE CombinePolyData.cxx)
 
target_link_libraries(CombinePolyData ${VTK_LIBRARIES})
```

**Download and Build CombinePolyData**

Click [here to download CombinePolyData](https://github.com/lorensen/VTKWikiExamplesTarballs/raw/master/CombinePolyData.tar) and its *CMakeLists.txt* file.
Once the *tarball CombinePolyData.tar* has been downloaded and extracted,
```
cd CombinePolyData/build 
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
./CombinePolyData
```
**WINDOWS USERS PLEASE NOTE:** Be sure to add the VTK bin directory to your path. This will resolve the VTK dll's at run time.
