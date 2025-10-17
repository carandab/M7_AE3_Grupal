# M7\_AE3\_ABPRO - Ejercicio Grupal: Plataforma de Gestión Académica con Django ORM 📚

Este proyecto es la solución al ejercicio grupal **M7\_AE3\_ABPRO**, que consiste en el desarrollo del modelo de datos para una **Plataforma de Gestión Académica** utilizando el framework **Django**. El objetivo principal fue modelar las relaciones entre entidades como **Profesores**, **Cursos**, **Estudiantes**, e **Inscripciones**, aplicando las mejores prácticas de Django ORM.

***Nota Importante:*** Se ha incluido una imagen del Diagrama Entidad-Relación (ERD) de la actividad dentro de la carpeta **`media`**. Esto se realiza con el único fin de asegurar que la estructura del repositorio contemple la ruta de archivo necesaria para el campo **`Foto de perfil`** en el modelo **`Perfiles`**, evitando errores al inicializar la base de datos o ejecutar comandos en la *shell* de Django.

---

## 📝 Contexto y Objetivos

La actividad se centra en el diseño e implementación de un sistema de gestión académica, exigiendo la definición de modelos de datos con los siguientes tipos de relaciones:

1.  **Relación Muchos a Uno (ForeignKey)**: Un **`Profesor`** puede impartir varios **`Cursos`**, pero un **`Curso`** pertenece a un solo **`Profesor`**. Se debe incluir la regla de **borrado en cascada** (`on_delete=models.CASCADE`).
2.  **Relación Muchos a Muchos (ManyToManyField)**: Entre **`Estudiantes`** y **`Cursos`**. Esta debe manejarse a través de una **entidad intermedia** explícita llamada **`Inscripciones`**.
3.  **Relación Uno a Uno (OneToOneField)**: Cada **`Estudiante`** debe tener un **`Perfil`** asociado con información adicional.

El desarrollo se realizó en una aplicación de Django llamada **`academico`**.

---

## 🧱 Modelos de Datos Implementados

Los siguientes modelos fueron definidos en `models.py` para la aplicación **`academico`**, reflejando la estructura del ERD:

| Modelo | Descripción | Relación | Atributos clave de relación |
| :--- | :--- | :--- | :--- |
| **`Profesores`** | Representa al docente. | **1:N con Cursos** | - |
| **`Cursos`** | Representa la materia impartida. | **N:1 con Profesores** | `profesor` (`ForeignKey` con `on_delete=CASCADE`). |
| **`Estudiantes`** | Representa al alumno. | **N:M con Cursos** | - |
| **`Inscripciones`** | Entidad intermedia para N:M. | **N:M** (a través de `ForeignKey` a `Estudiantes` y `Cursos`) | `estudiante`, `curso`, `fecha_inscripcion`, `estado`, `nota_final` (opcional). |
| **`Perfiles`** | Información adicional del estudiante. | **1:1 con Estudiantes** | `estudiante` (`OneToOneField`), `biografia`, `foto`, `redes`. |

---

## 🖥️ Validación con la Shell de Django

El siguiente script fue utilizado en la *shell* de Django para validar la creación de instancias, la correcta asignación de relaciones y el funcionamiento del borrado en cascada.

### Forma de utilizar la Shell de Django

1.  Asegúrese de haber ejecutado las migraciones previamente:
    ```bash
    python manage.py makemigrations
    python manage.py migrate
    ```
2.  Ingrese a la *shell* de Python de Django desde la carpeta raíz de su proyecto:
    ```bash
    python manage.py shell
    ```
3.  Una vez dentro, puede pegar y ejecutar el siguiente script paso a paso.

### Script de Validación de Queries

Este script cumple con la creación de profesores, cursos, estudiantes, perfiles e inscripciones, la modificación de estados y notas, y la comprobación del borrado en cascada.

```python
from academico.models import Profesores, Cursos, Estudiantes, Perfiles, Inscripciones
from datetime import date

# 1. CREAR DOS PROFESORES Y ASIGNARLES VARIOS CURSOS (Relación 1:N)

p1 = Profesores.objects.create(
    nombre="Juan Pérez",
    email="juan.perez@escuela.cl"
)

p2 = Profesores.objects.create(
    nombre="María González",
    email="maria.gonzalez@escuela.cl"
)

# Cursos asignados a p1 (Juan Pérez)
c1 = Cursos.objects.create(
    nombre="Python Básico",
    profesor=p1,
    descripcion="Introducción a Python"
)

c2 = Cursos.objects.create(
    nombre="Django Intermedio",
    profesor=p1,
    descripcion="Desarrollo web con Django"
)

c3 = Cursos.objects.create(
    nombre="Django Avanzado",
    profesor=p1,
    descripcion="Técnicas avanzadas en Django"
)

# Cursos asignados a p2 (María González)
c4 = Cursos.objects.create(
    nombre="HTML y CSS",
    profesor=p2,
    descripcion="Fundamentos de desarrollo web"
)

c5 = Cursos.objects.create(
    nombre="JavaScript Moderno",
    profesor=p2,
    descripcion="JavaScript ES6+"
)

# 2. CREAR ESTUDIANTES E INSCRIBIRLOS EN DIFERENTES CURSOS (Relación N:M a través de Inscripciones)

e1 = Estudiantes.objects.create(
    nombre="Pedro López",
    email="pedro.lopez@estudiante.cl"
)

e2 = Estudiantes.objects.create(
    nombre="Ana Martínez",
    email="ana.martinez@estudiante.cl"
)

e3 = Estudiantes.objects.create(
    nombre="Carlos Ramírez",
    email="carlos.ramirez@estudiante.cl"
)

# Inscripciones
i1 = Inscripciones.objects.create(
    estudiante=e1,
    curso=c1,
    estado="activo"
)

i2 = Inscripciones.objects.create(
    estudiante=e1,
    curso=c4,
    estado="activo"
)

i3 = Inscripciones.objects.create(
    estudiante=e2,
    curso=c1,
    estado="activo"
)

i4 = Inscripciones.objects.create(
    estudiante=e2,
    curso=c2,
    estado="activo"
)

i5 = Inscripciones.objects.create(
    estudiante=e2,
    curso=c5,
    estado="activo"
)

i6 = Inscripciones.objects.create(
    estudiante=e3,
    curso=c2,
    estado="activo"
)

i7 = Inscripciones.objects.create(
    estudiante=e3,
    curso=c3,
    estado="activo"
)

# 3. MODIFICAR ESTADOS DE INSCRIPCIONES Y AGREGAR NOTAS FINALES

i1.estado = "finalizado"
i1.nota_final = 6.5
i1.save()

i3.estado = "finalizado"
i3.nota_final = 7.0
i3.save()

i4.nota_final = 5.8
i4.save()

i7.estado = "finalizado"
i7.nota_final = 4.5
i7.save()

# 4. CREAR PERFILES PARA LOS ESTUDIANTES (Relación 1:1)

pe1 = Perfiles.objects.create(
    estudiante=e1,
    biografia="Estudiante apasionado por el desarrollo backend",
    redes="[https://github.com/pedrolopez](https://github.com/pedrolopez)"
)

pe2 = Perfiles.objects.create(
    estudiante=e2,
    biografia="Entusiasta del desarrollo full-stack",
    redes="[https://linkedin.com/in/anamartinez](https://linkedin.com/in/anamartinez)"
)

pe3 = Perfiles.objects.create(
    estudiante=e3,
    biografia="Aprendiendo desarrollo web desde cero",
    redes="[https://github.com/carlosramirez](https://github.com/carlosramirez)"
)

# 5. COMPROBAR QUE EL BORRADO EN CASCADA FUNCIONA

print(f"Cursos antes de borrar a p1: {Cursos.objects.all().count()}") 
p1.delete()
print(f"Cursos después de borrar a p1: {Cursos.objects.all().count()}") 
print(f"Inscripciones después de borrar a p1: {Inscripciones.objects.all().count()}") 

print(f"Perfiles antes de borrar a e3: {Perfiles.objects.all().count()}")
e3.delete()
print(f"Perfiles después de borrar a e3: {Perfiles.objects.all().count()}")
````

-----

## 👥 Integrantes del Grupo

| Nombre | GitHub Handle |
| :--- | :--- |
| **Cecilia Ramos** | `@cecyramos` |
| **Cristian Aranda** | `@carandab` |
| **Nathalia Rojas** | `@Nathalia-Rojas` |

-----

## 🔗 Repositorio del Proyecto

El código completo del proyecto se encuentra disponible en:
[https://github.com/carandab/M7\_AE3\_Grupal](https://github.com/carandab/M7_AE3_Grupal)

```
```
