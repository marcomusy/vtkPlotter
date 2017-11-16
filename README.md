![vtk](https://www.vtk.org/wp-content/uploads/2015/03/vtk_logo-main1.png)
# vtkPlotter
A python helper class to easily draw tridimensional objects.

Based on VTK (tested on versions 5.8, 6.1, 7.0): https://www.vtk.org and *numpy*
<br />

## Download:
```bash
git clone https://github.com/marcomusy/vtkPlotter.git
cd vtkPlotter
```

## Example usage:<br />

Simple command line usage:
```bash
python plotter.py data/*.vtk  # valid formats: [vtp,ply,obj,stl,xml,pcd]
```

Run a tutorial script:
>*python example.py*<br />

```python
import plotter

# Declare an instance of the class
vp = plotter.vtkPlotter()
vp.help() # shows a help message
```
<br />

Load a vtk file as a vtkActor and visualize it in wireframe style. <br />
The tridimensional shape corresponds to the outer shape of the embryonic mouse limb at about 12 days of gestation.<br />
Press *Esc* to close the window and exit python session or *q* to continue:
```python
vp = plotter.vtkPlotter()
actor = vp.load('data/290.vtk', wire=1)
vp.show()
#vp.show(actor)           # overrides the content of vp.actors
#vp.show(actors=[actor])  # same as above
```
![ex1](https://user-images.githubusercontent.com/32848391/32666968-908d1bf6-c639-11e7-9201-46572a2349c2.png)
<br />

Load 3 actors assigning each a different color, use their file names as legend entries.
No need to use any variables, as actors are stored internally in vp.actors:
```python
vp = plotter.vtkPlotter()
vp.load('data/250.vtk', c=(1,0.4,0)) # c=(R,G,B) color
vp.load('data/270.vtk', c=(1,0.6,0))
vp.load('data/290.vtk', c=(1,0.8,0))
print 'Loaded vtkActors: ', len(vp.actors)
vp.show(legend=vp.files)
```
![ex2](https://user-images.githubusercontent.com/32848391/32666969-90a7dc48-c639-11e7-8795-b139166f0504.png)
<br />

Draw a spline that goes through a set of points, don't show the points *(nodes=False)*:
```python
from random import uniform as u
pts = [(u(0,1), u(0,1), u(0,1)) for i in range(10)]
vp = plotter.vtkPlotter()
vp.spline(pts, s=.01, nodes=False)
vp.show()
```
![ex3](https://user-images.githubusercontent.com/32848391/32666970-90c1b38e-c639-11e7-92dd-336f2aa2a2cf.png)
<br />


Draw a PCA ellipsoid that contains 67% of a cloud of points:
```python
pts = [(u(0,200), u(0,200), u(0,200)) for i in range(50)]
vp = plotter.vtkPlotter()
vp.points(pts)
vp.ellipsoid(pts, pvalue=0.67)
vp.show()
```
![pca](https://user-images.githubusercontent.com/32848391/32732169-12f82a5a-c88c-11e7-9a31-f14b100374cb.png)
<br />


Show 3 planes as a grid, add a dummy sine plot on top left,
add 3 axes at the origin:
```python
#Show 3 planes as a grid, add a dummy sine plot on top left, add 3 axes at the origin:
import numpy as np
xycoords = [(np.exp(i/10.), np.sin(i/5.)) for i in range(40)]
vp = plotter.vtkPlotter()
vp.xyplot( xycoords )
vp.grid(center=(0,0.5,0.5), normal=(1,0,0), c=(1,0,0))
vp.grid(center=(0.5,0,0.5), normal=(0,1,0), c=(0,1,0))
vp.grid(center=(0.5,0.5,0), normal=(0,0,1), c=(0,0,1))
vp.axes()
vp.show(axes=0)
```
![ex4](https://user-images.githubusercontent.com/32848391/32666971-90dac112-c639-11e7-96ef-ec41bdf3b7e5.png)
<br />

Show the vtk boundaries of a vtk surface and its normals<br />
(*ratio* reduces the total nr of arrows by the indicated factor):
```python
vp = plotter.vtkPlotter()
va = vp.load('data/290.vtk', c=(1,0.1,0.1))
vp.normals(va, ratio=5)
vp.boundaries(va)
vp.show()
```
![ex5](https://user-images.githubusercontent.com/32848391/32666972-90f46a5e-c639-11e7-93c3-e105322ff481.png)
<br />


Split window in a 36 subwindows and draw something in
windows nr 12 and nr 33. Then open an independent window and draw on two shapes:
```python
vp1 = plotter.vtkPlotter(shape=(6,6), size=(900,900))
vp1.renderers[35].SetBackground(.8,.9,.9)
v270 = vp1.load('data/270.vtk') #load as vtkActor
v290 = vp1.loadPoly('data/290.vtk') #load as polydata
vp1.interactive = False
vp1.show(at=12, actors=[v270,v290]) # polys are automatically  
vp1.show(at=33, actors=[v270,v290]) # transformed into actors
vp2 = plotter.vtkPlotter(bg=(0.9,0.9,1))
v250 = vp2.load('data/250.vtk')
v270 = vp2.load('data/270.vtk')
vp2.show()
```
![ex6](https://user-images.githubusercontent.com/32848391/32666973-910d6dc4-c639-11e7-9645-e19ffdfff3d1.png)
<br />


Load a surface and show its curvature based on 4 different schemes. All four shapes
share a common vtkCamera:<br />
*0-gaussian, 1-mean, 2-max, 3-min*
```python
vp = plotter.vtkPlotter(shape=(1,4), size=(400,1600))
v = vp.load('data/290.vtk')
vp.interactive = False
for i in [0,1,2,3]:
    c = vp.curvatures(v, ctype=i, r=1, alpha=0.8)
    vp.show(at=i, actors=[c])
vp.interact() # same as setting flag interactive=True
```
![ex7](https://user-images.githubusercontent.com/32848391/32666974-912de586-c639-11e7-880a-2b377cde3615.png)
<br />


Draw a bunch of simple objects on separate parts of the rendering window:
```python
vp = plotter.vtkPlotter(shape=(2,3), size=(800,1200))
vp.commoncam   = False
vp.interactive = False
vp.show(at=0, actors=vp.arrow( [0,0,0], [1,1,1] ))
vp.show(at=1, actors=vp.line(  [0,0,0], [1,2,3] ))
vp.show(at=2, actors=vp.points( [ [0,0,0], [1,1,1], [3,1,2] ] ))
vp.show(at=3, actors=vp.text('hello', cam=False))
vp.show(at=4, actors=vp.sphere([.5,.5,.5], r=0.3))
vp.show(at=5, actors=vp.cube(  [.5,.5,.5], r=0.3))
vp.interact()
```
![ex8](https://user-images.githubusercontent.com/32848391/32666975-91690102-c639-11e7-8f7b-ad07bd6019da.png)
<br />


Draw a line in 3D that fits a cloud of points,
also show the first set of 20 points and fit a plane to them:
```python
for i in range(500): # draw 500 fit lines superposed
    x = np.mgrid[-2:5 :20j][:, np.newaxis] # generate 20 points
    y = np.mgrid[ 1:9 :20j][:, np.newaxis]
    z = np.mgrid[-5:3 :20j][:, np.newaxis]
    data  = np.concatenate((x, y, z), axis=1)
    data += np.random.normal(size=data.shape)*0.8 # add gauss noise
    if i==0:
        vp.points(data)
        vp.fitplane(data)
    vp.fitline(data, lw=10, alpha=0.01) # fit
print vp.result['slope'] # access the last fitted slope direction
vp.show()
```
![plane](https://user-images.githubusercontent.com/32848391/32667173-3ad163ec-c63a-11e7-8b3d-4a8ba047eae9.png)
<br />


Display a tetrahedral mesh (Fenics/Dolfin format). The internal verteces are displayed too:
```python
vp.load('data/290.xml.gz', wire=1)
vp.show()        
```
![ex9](https://user-images.githubusercontent.com/32848391/32666976-918480bc-c639-11e7-9749-4fd0b71523ad.png)
<br />


As a short cut, the filename (or a list of filenames) can be given in the show command directly:
```python
plotter.vtkPlotter().show('data/limb.pcd') # Point cloud (PCL file format)
```
![pcd](https://user-images.githubusercontent.com/32848391/32798156-287955b4-c974-11e7-9abf-6057dd43c5db.png)
<br />

If you need to do more complicated things (define widgets.. etc), you can still access all the 
usual VTK objects like interactors and renderers through *vp.interactor, vp.renderer*... etc.<br />

To install plotter.py in a fixed location (e.g. *$HOME/soft/bin*):
```bash
mv vtkPlotter $HOME/soft/bin
```
 add this lines to your .bashrc:
```bash
export PYTHONPATH=$HOME/soft/bin/vtkPlotter:$PYTHONPATH
export PATH=$HOME/soft/bin/vtkPlotter:$PATH
```
