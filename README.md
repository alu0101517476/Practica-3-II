# Practica-3-II

Autor: Eric Bermúdez Hernández

Email: alu0101517476@ull.edu.es

----

# Índice


----

**Descripción del trabajo realizado**


# 1. Ejercicio Físicas 3D

---

# 2. Ejercicio Físicas 3D - Scripts

### **Ejercicio 1**

Para resolver el ejercicio, como personaje utilicé un cubo al personaje, al cual le añadí un Rigidbody el cual tiene gravedad y marcadas las casillas de *Freeze Rotation*. Después le añadí el siguiente Script:

```C#
using UnityEngine;
using UnityEngine.InputSystem; // Nuevo Input System

[RequireComponent(typeof(Rigidbody))]
public class RigidbodyCharacterController_NewInput : MonoBehaviour
{
    [Min(0.1f)] public float moveSpeed = 5f;

    private Rigidbody rb;
    private Vector3 inputDirWorld;

    void Awake()
    {
        rb = GetComponent<Rigidbody>();
        rb.interpolation = RigidbodyInterpolation.Interpolate;
        rb.constraints = RigidbodyConstraints.FreezeRotation;
    }

    void Update()
    {
        // Leer las teclas WASD o Flechas desde el nuevo sistema
        float ix = 0f, iz = 0f;

        if (Keyboard.current != null)
        {
            if (Keyboard.current.aKey.isPressed || Keyboard.current.leftArrowKey.isPressed)
                ix -= 1f;
            if (Keyboard.current.dKey.isPressed || Keyboard.current.rightArrowKey.isPressed)
                ix += 1f;
            if (Keyboard.current.wKey.isPressed || Keyboard.current.upArrowKey.isPressed)
                iz += 1f;
            if (Keyboard.current.sKey.isPressed || Keyboard.current.downArrowKey.isPressed)
                iz -= 1f;
        }

        Vector3 raw = new Vector3(ix, 0f, iz);
        inputDirWorld = raw.sqrMagnitude > 1f ? raw.normalized : raw;
    }

    void FixedUpdate()
    {
        // Aplicar velocidad al Rigidbody
        Vector3 desiredHorizontalVel = inputDirWorld * moveSpeed;
        Vector3 currentVel = rb.linearVelocity;
        rb.linearVelocity = new Vector3(desiredHorizontalVel.x, currentVel.y, desiredHorizontalVel.z);
    }
}

```

De esta manera el movimiento del cubo está influenciado por un Rigidbody, pero manteniendo el control total. En el siguiente GIF se puede comprobar el funcionamiento del Script:

# **PONER AQUÍ GIF DEL PRIMER EJERCICIO**
