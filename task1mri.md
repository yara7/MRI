# Task_1 MRI.
## Hadeel sameh Hassan 
## Yara Metwally
## Heba Ali
## Menna Tollah Rafat

# bulk gif
```python
import numpy as np
from cv2 import cv2
import pyglet
#bulk gif
animation = pyglet.image.load_animation('MRI_task1.gif')
animSprite = pyglet.sprite.Sprite(animation)
w = animSprite.width
h = animSprite.height
window = pyglet.window.Window(width=w, height=h)
r,g,b,alpha = 0.5,0.5,0.8,0.5
pyglet.gl.glClearColor(r,g,b,alpha)
@window.event
def on_draw():
    window.clear()
    animSprite.draw()
pyglet.app.run()
```

## loading image(phantom) ,getting FT,and mapping intenisties into t1,t2 for brain
```python
from matplotlib import pyplot as plt
import cv2 as cv
import numpy as np
img = cv.imread("fantom.PNG",0)
      
f = np.fft.fft2(img)
fshift = np.fft.fftshift(f)
magnitude_spectrum = 20*np.log(np.abs(fshift))
size=len(img)
intensity_list=img
T1_list=[]
T1_list_sub=[]
for k in range(0,size-1):
    temp=intensity_list[k]
    for i in temp:
            if (0<=temp[i]<=50):
                T1=2000
            elif (51<=temp[i]<=100):
                T1=800
            elif (101<=temp[i]<=200):
                T1=650
            elif (201<=temp[i]<=255):
                T1=250
            T1_list_sub.append(T1)
    T1_list.append(T1_list_sub)
print(T1_list)
T2_list=[]
T2_list_sub=[]
for k in range(0,size-1):
    temp=intensity_list[k]
    for i in temp:
            if (0<=temp[i]<=50):
                T2=80
            elif (51<=temp[i]<=100):
                T2=90
            elif (101<=temp[i]<=200):
                T2=100
            elif(201<=temp[i]<=255):
                T2=150
            T2_list_sub.append(T2)
    T2_list.append(T2_list_sub)
print(T2_list)
plt.subplot(121),plt.imshow(img, cmap = 'gray')
plt.title('Input Image'), plt.xticks([]), plt.yticks([])
plt.subplot(122),plt.imshow(magnitude_spectrum, cmap = 'gray')
plt.title('Magnitude Spectrum'), plt.xticks([]), plt.yticks([])
plt.show()
```


## random B in z direction
```python
import matplotlib.pyplot as plt
import matplotlib as mpl
import numpy as np
from cv2 import cv2
import pyglet
#random B in z direction
z=np.arange(0,10,0.1)
amplitude=np.random.random(len(z))
plt.plot(z,amplitude,color='b')
plt.ylabel('amplitude',fontsize=14,color='red')
plt.xlabel('Z direction',fontsize=14,color='red')
plt.title("Random B signal")
plt.show()
```
## cpp code for generating bulk trajectory (which we record  the gif from)
# (this code requires opengl library)
```cpp
#include<windows.h>
#ifdef __APPLE__
#include <GLUT/glut.h>
#else
#include <GL/glut.h>
#endif
#include <stdlib.h>
#include <math.h>
#include <GL/glut.h>
static int d =0,y=2;
float x=0;
void init(void)
{
    glClearColor(1.0, 1.0, 1.0, 0.0);
    glEnable(GL_DEPTH_TEST);

}
void display(void)
{
    glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
    glColor3f(1.0, 0.0, 0.0);
    glPushMatrix();
    glPushMatrix();
    glRotatef(d,0.0,1.0,0);
    glRotatef(x,0.0,0.0,1.0);
    glPushMatrix();
    glColor3f(1.0, 0.0, 1.0);
    glPushMatrix();
    glTranslatef(0.3,y,0);
    glutSolidSphere(0.2, 20, 8);
    glPopMatrix();
    glBegin(GL_LINES);
    glVertex3f(0.3,y,0);
    glVertex3f(0,0,0);
    glEnd();
    glPopMatrix();
    glPopMatrix();
    glPushMatrix();
    glColor3f(0.0, 0.0, 0.0);
    glBegin(GL_LINES);
    glVertex3f(0,0,-40);
    glVertex3f(0,0,40);
    glVertex3f(0,0,0);
    glEnd();
    glBegin(GL_LINES);
    glVertex3f(0,40,0);
    glVertex3f(0,0,0);
    glEnd();
    glBegin(GL_LINES);
    glVertex3f(-40,0,0);
    glVertex3f(40,0,0);
    glVertex3f(0,0,0);
    glEnd();
    glPopMatrix();
    glPopMatrix();
    glutSwapBuffers();
}
void timer(int value)
{
    if (value == 0)
    {
        d = (d + 1) % 360;
        glutPostRedisplay();
        glutTimerFunc(10, timer, 0);

    }
    else if (value == 1)
    {
        x=x+90;
        glutPostRedisplay();
        glutTimerFunc(50, timer, 0);
    }
    else if (value == 2)
    {
        x=x-10;
        glutPostRedisplay();
        glutTimerFunc(50, timer, 0);
    }

}

void reshape(int w, int h)
{
    glViewport(0, 0, (GLsizei)w, (GLsizei)h);
    glMatrixMode(GL_PROJECTION);
    glLoadIdentity();
    gluPerspective(70.0, (GLfloat)w / (GLfloat)h, 1.0, 20.0);
    glMatrixMode(GL_MODELVIEW);
    glLoadIdentity();
    gluLookAt(5.0, 5.0, 5.0, 0.0, 0.0, 0.0, 0.0, 1.0, 0.0);

}
void keyboard(unsigned char key, int x, int y)
{
switch (key){
    case 's':
        x=90;
        glutTimerFunc(0, timer, 1);
        glutPostRedisplay();
        break;
    case 'z':
        glutTimerFunc(0, timer, 2);
        glutPostRedisplay();
        break;
    case 27:
        exit(0);
        break;
    default:
        break;
}
}
int main(int argc, char **argv)
{
    glutInit(&argc, argv);
    glutInitDisplayMode(GLUT_DOUBLE | GLUT_RGB);
    glutInitWindowSize(500, 500);
    glutInitWindowPosition(500, 500);
    glutCreateWindow("MRI Bloch Equation simulation");
    glutKeyboardFunc(keyboard);
    glutTimerFunc(0, timer, 0);
    init();
    glutDisplayFunc(display);
    glutReshapeFunc(reshape);
    glutMainLoop();
    return 0;
}
```