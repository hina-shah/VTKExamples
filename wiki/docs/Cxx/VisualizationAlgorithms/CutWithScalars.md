[VTKExamples](Home)/[Cxx](Cxx)/VisualizationAlgorithms/CutWithScalars

<img align="right" src="https://github.com/lorensen/VTKExamples/raw/master/Testing/Baseline/VisualizationAlgorithms/TestCutWithScalars.png" width="256" />

**CutWithScalars.cxx**
```c++
#include <vtkSmartPointer.h>
#include <vtkXMLPolyDataReader.h>
#include <vtkPolyDataMapper.h>
#include <vtkPolyData.h>
#include <vtkDoubleArray.h>
#include <vtkPoints.h>
#include <vtkPointData.h>
#include <vtkPlane.h>
#include <vtkProperty.h>
#include <vtkActor.h>
#include <vtkCamera.h>
#include <vtkRenderer.h>
#include <vtkRenderWindow.h>
#include <vtkRenderWindowInteractor.h>

#include <vtkContourFilter.h>

int main(int argc, char *argv[])
{
  if (argc < 2)
  {
    std::cout << "Usage: " << argv[0]
              << " inputFilename(.vtp) [numberOfCuts]" << std::endl;
    return EXIT_FAILURE;
  }
  std::string inputFilename = argv[1];

  int numberOfCuts = 10;
  if (argc > 2)
  {
    numberOfCuts = atoi(argv[2]);
  }

  vtkSmartPointer<vtkXMLPolyDataReader> reader =
    vtkSmartPointer<vtkXMLPolyDataReader>::New();
  reader->SetFileName(inputFilename.c_str());
  reader->Update();

  double bounds[6];
  reader->GetOutput()->GetBounds(bounds);
  std::cout << "Bounds: "
            << bounds[0] << ", " << bounds[1] << " "
            << bounds[2] << ", " << bounds[3] << " "
            << bounds[4] << ", " << bounds[5] << std::endl;

  vtkSmartPointer<vtkPlane> plane =
    vtkSmartPointer<vtkPlane>::New();
  plane->SetOrigin((bounds[1] + bounds[0]) / 2.0,
                   (bounds[3] + bounds[2]) / 2.0,
                   (bounds[5] + bounds[4]) / 2.0);
  plane->SetNormal(0,0,1);

  // Create Scalars
  vtkSmartPointer<vtkDoubleArray> scalars =
    vtkSmartPointer<vtkDoubleArray>::New();
  int numberOfPoints = reader->GetOutput()->GetNumberOfPoints();
  scalars->SetNumberOfTuples(numberOfPoints);
  vtkPoints *pts = reader->GetOutput()->GetPoints();
  for (int i = 0; i < numberOfPoints; ++i)
  {
    double point[3];
    pts->GetPoint(i, point);
    scalars->SetTuple1(i, plane->EvaluateFunction(point));
  }
  reader->GetOutput()->GetPointData()->SetScalars(scalars);
  reader->GetOutput()->GetPointData()->GetScalars()->GetRange();

  // Create cutter
  vtkSmartPointer<vtkContourFilter> cutter =
    vtkSmartPointer<vtkContourFilter>::New();
  cutter->SetInputConnection(reader->GetOutputPort());
  cutter->ComputeScalarsOff();
  cutter->ComputeNormalsOff();
  cutter->GenerateValues(
    numberOfCuts,
    .99 * reader->GetOutput()->GetPointData()->GetScalars()->GetRange()[0],
    .99 * reader->GetOutput()->GetPointData()->GetScalars()->GetRange()[1]);

  vtkSmartPointer<vtkPolyDataMapper> cutterMapper =
    vtkSmartPointer<vtkPolyDataMapper>::New();
  cutterMapper->SetInputConnection( cutter->GetOutputPort());
  cutterMapper->ScalarVisibilityOff();

  // Create cut actor
  vtkSmartPointer<vtkActor> cutterActor =
    vtkSmartPointer<vtkActor>::New();
  cutterActor->GetProperty()->SetColor(1.0,1,0);
  cutterActor->GetProperty()->SetLineWidth(2);
  cutterActor->SetMapper(cutterMapper);

  // Create model actor
  vtkSmartPointer<vtkPolyDataMapper> modelMapper =
    vtkSmartPointer<vtkPolyDataMapper>::New();
  modelMapper->SetInputConnection( reader->GetOutputPort());
  modelMapper->ScalarVisibilityOff();

  vtkSmartPointer<vtkActor> modelActor =
    vtkSmartPointer<vtkActor>::New();
  modelActor->GetProperty()->SetColor(0.5,1,0.5);
  modelActor->SetMapper(modelMapper);

  // Create renderers and add actors of plane and model
  vtkSmartPointer<vtkRenderer> renderer =
    vtkSmartPointer<vtkRenderer>::New();
  renderer->AddActor(cutterActor);
  renderer->AddActor(modelActor);

  // Add renderer to renderwindow and render
  vtkSmartPointer<vtkRenderWindow> renderWindow =
    vtkSmartPointer<vtkRenderWindow>::New();
  renderWindow->AddRenderer(renderer);
  renderWindow->SetSize(600, 600);

  vtkSmartPointer<vtkRenderWindowInteractor> interactor =
    vtkSmartPointer<vtkRenderWindowInteractor>::New();
  interactor->SetRenderWindow(renderWindow);
  renderer->SetBackground(.2, .3, .4);


  renderer->GetActiveCamera()->SetPosition(0, -1, 0);
  renderer->GetActiveCamera()->SetFocalPoint(0, 0, 0);
  renderer->GetActiveCamera()->SetViewUp(0, 0, 1);
  renderer->GetActiveCamera()->Azimuth(30);
  renderer->GetActiveCamera()->Elevation(30);

  renderer->ResetCamera();
  renderWindow->Render();

  interactor->Start();

  return EXIT_SUCCESS;
}
```
**CMakeLists.txt**
```cmake
cmake_minimum_required(VERSION 2.8)
 
PROJECT(CutWithScalars)
 
find_package(VTK REQUIRED)
include(${VTK_USE_FILE})
 
add_executable(CutWithScalars MACOSX_BUNDLE CutWithScalars.cxx)
 
target_link_libraries(CutWithScalars ${VTK_LIBRARIES})
```

**Download and Build CutWithScalars**

Click [here to download CutWithScalars](https://github.com/lorensen/VTKWikiExamplesTarballs/raw/master/CutWithScalars.tar) and its *CMakeLists.txt* file.
Once the *tarball CutWithScalars.tar* has been downloaded and extracted,
```
cd CutWithScalars/build 
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
./CutWithScalars
```
**WINDOWS USERS PLEASE NOTE:** Be sure to add the VTK bin directory to your path. This will resolve the VTK dll's at run time.
