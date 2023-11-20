This repo is part of a school project and is only intended for my teacher to see. Please ignore.
# pytracerSorce
kurze pytracer codeabschnitte mit erklärung

# Rays aussenden
```python
# stark auf python basierender pseudocode 
def render():
  # loop through every pixel
  for y in range(image_height):
    for x in range(image_width):
      # add offset to upper left pixelcenter by subrracting coord
      [point] pixel_center = upper_left_pixel_center + (x * px_distance.x) + (y * px_distance.y)
      
      # get vector from camera-focalpoint to pixelcenter by subtracting coords
      [vec] ray_direction = px_center - camera_center
      
      # create Ray object
      [ray] ray = Ray(position=pixel_center, direction=ray_direction)
      if ray.collide_with_world():
        # show red
        pixel(x, y).set_color(RED)
      else:
        pixel(x, y).set_color(BLACK)
```
Diese Funktion wird genutzt um Rays aus der Kamera zu schicken.  
Dazu wird über jeden Pixel iteriert und zunächst der Mittelpunkt berechnet, 
indem der Vektor upper_left_pixel_center um die Pixelabstände px_distance.x und px_distance.y entsprechend verschoben wird. 
Dann wird ein richtungsvektor vom focal point der Kamera zu diesem Mittelpunkt bestimmt.   
Dieser wird als Vektorfunktion mit dem Mittelpunkt als Ortsvektor in ein Ray objekt umgewandelt.
In diesem Beispiel wird mittels einer abstrakten Funktion "ray.collide_with_world()" getestet ob der ray mit anderen objekten
kollidiert. Davon abhängig wird eine Pixelfarbe gesetzt.
Wenn ein Zusammenstoß mit Objekten in der Welt vorliegt, wird die Pixel-Farbe auf Rot (RED) gesetzt, andernfalls auf Schwarz (BLACK).


# Rays in der Szene herumspringen lassen 
```python
def get_ray_color(ray, bounces_left):
  # exit if max bounce count is reached
  if bounces_left <= 0:
    return color(0,0,0) # color as r,g,b with range 0-1
  
  newRay = ray.scatter() # returns the ray resulting from a collision (the reflected ray)
  
  if no_collision:
    return sky_color

  # same as: scatter_color = col_obj1.color * col_obj2.color * ... * BLACK/Skycolor 
  scatter_color = collided_obj_color * get_ray_color(newRay, bounces_left - 1)
  
  return collided_obj_color
```
Diese Funktion "get_ray_color" berechnet die resultierende Farbe eines Rays in einer Szene.  
Die Funktion nimmt zwei Parameter entgegen: ray, der den ursprünglichen Strahl repräsentiert, und bounces_left, der die maximale Anzahl der Reflexionsstufen angibt.  
- Abbruchbedingung: Wenn die maximale Anzahl der Reflexionen erreicht ist (bounces_left <= 0), wird die Farbe Schwarz zurückgegeben.  
- Reflexion des Strahls: Ein neuer Strahl (newRay) wird durch die Methode scatter() erzeugt, die den reflektierten Strahl nach einer Kollision abhängig vom Material zurückgibt.  
- Kollisionsüberprüfung: Falls keine Kollision passiert ist (no_collision == True), wird die Hintergrundfarbe (sky_color) zurückgegeben.  
- Rekursive Berechnung: Die Funktion ruft sich rekursiv mit dem reflektierten Strahl (newRay) und einer verringerten Anzahl verbleibender Reflexionen (bounces_left - 1) auf.  
- Farbberechnung: Die resultierende Farbe wird mit der Farbe des kollidierten Objekts (collided_obj_color) multipliziert, um die Lichtabsorption des Materials zu berücksichtigen. Die rekursion wiederholt den schritt für alle kollidierten objekte auf dem Weg des Rays. 
- Rückgabewert: Die berechnete Farbe wird zurückgegeben.

# Ray Objekt

```python
class Ray:
    def __init__(self, origin:point3, direction:vec3) -> None:
        self.origin = origin
        self.direction = direction

    def getOrigin(self):
        return self.origin
    
    def getDirection(self):
        return self.direction
    
    def step(self, t):
        return self.origin + (t * self.direction)
```
Hier sieht man die Ray-Klasse mit den Member-Variablen origin (Ortsvektor) und direction (Richtungsvektor).   
Zusätzlich verfügt die Klasse über 'get'-Methoden, um auf diese zuzugreifen, und eine 'step'-Methode, die einen Punkt auf dem Strahl sampelt.

# Vektor klasse
```python
class vec3:
    def __init__(self, x, y, z) -> None:
        self.x = x
        self.y = y
        self.z = z
    
    def __add__(self, o):
        return vec3(
            self.x + o.x,
            self.y + o.y,
            self.z + o.z
        )
    
    def __iadd__(self, o):
        return self + o

    ...

    @staticmethod
    def random(minV=None, maxV=None):
        if minV and maxV:
            return vec3(random.uniform(minV, maxV), random.uniform(minV, maxV), random.uniform(minV, maxV))
        else:
            return vec3(random.uniform(), random.uniform(), random.uniform())
```
Hier sieht man einen Ausschnitt aus der Vektor-Klasse. Sie implementiert von Python bereitgestellte "Magic Methods" wie __iadd__, __imul__ etc., die die Operatoren überladen,
so dass man direkt zwei Klasseninstanzen verrechnen kann (z.B., vec1 + vec2 wäre sonst nicht möglich). Außerdem mache ich Gebrauch von statischen Methoden wie random.
Statische Methoden sind klassen- und nicht instanzgebunden (also nicht vec1 = Vec3(0,0,0); Vec3.func() sondern Vec3.func()).
Außerdem besitzt sie natürlich Member-Variablen für die Komponenten des Vektors.

# Punkte
```python
class point3(vec3):
    def __init__(self, x, y, z) -> None:
        super().__init__(x, y, z)
```
Alternativ gibt es auch eine Point3-Klasse für Punkte. Da Punkte im Grunde genommen dasselbe sind wie Ortsvektoren, vererbe ich einfach alle Eigenschaften der Vektorenklasse, sodass ich sie ebenso auch für Punkte nutzen kann.

# Rekursion 
```python
def countdown(n):
    print(n)
    if n > 0:
        countdown(n - 1)

countdown(5)
```
Das war mein Beispiel für eine einfache rekursive Funktion in Python.    
Sie nimmt einen Wert, gibt diesen aus und ruft sich selbst mit dem Wert auf, allerdings um eins verringert.   
Die Abbruchbedingung ist hier, wenn der Wert zu klein wird. Dann wird die Rekursion gebrochen, und die Funktion wird beendet. 
