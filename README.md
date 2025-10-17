# Practica-3-II

Autor: Eric Bermúdez Hernández

Email: alu0101517476@ull.edu.es

----

**Descripción del trabajo realizado**


# 1. Ejercicio Físicas 3D

### **Situación 1**

En esta situación, al tener el cubo un Rigidbody pero los demás elementos no, el cubo cae y se detiene al entrar en contacto con el plano, mientras que al plano y a la esfera no les pasa nada

### **Situación 2**

En esta situación tanto la esfera como el cubo caen y se detienen ante al plano y se desplazan si tanto la esfera como el cubo llegan a tocarse. Por otra parte, al plano no le pasa nada

### **Situación 3**

En esta situación el cubo cae. La Esfera no es empujada por la gravedad ni fuerzas, pero sí participa en colisiones si la atraviesan, es decir, los objetos físicos rebotan y chocan contra ella. Si no la mueves, se queda flotando. Esto ocurre ya que la gravedad se ignora al ser un objeto cinemático

### **Situación 4**

Al aplicar lo que dicta la situación, ocurre lo mismo que en la situación 2, con la diferencia de que el plano ahora también es un Rigidbody inmóvil. Cabe destacar que el plano caería hacia el infinito junto con los demás objetos, por lo tanto, si queremos el comportamiento descrito, el cual es similar al del ejercicio 2, tenemos que marcar la opción `Is Kinematic`

### **Situación 5**

En esta situación, todos  los objetos caen. La Esfera tendrá más inercia, por lo que, en un choque entre la Esfera y el Cubo, la esfera impone más su movimiento, es decir, hay un menor cambio de velocidad para ella

### **Situación 6**

En la presente situación, se presenta un escenario muy similar al de la situación anterior con la diferencia de que la esfera practicamente no se inmuta ante los impactos

### **Situación 7**

Para esta situación se crea un material con alta fricción y se le asigna a la esfera. En este caso, todo es muy similar a las situaciones anteriores, con la diferencia que la esfera reduce muchísimo su deslizamiento enn el plano, respecto a las situaciones anteriores

### **Situación 8**

En este caso, el cubo puede atravesar la esfera pero saldrán eventos de trigger. Para ver estos eventos, tendríamos que asignarlae a la esfera un Script que al atravesarla dispare algún evento

### **Ejercicio 9**

La Esfera cae al ser física, pero no colisiona sólidamente con el Plano ni con el Cubo al ser también trigger. Además, disparará eventos de trigger al atravesarlos de la misma manera que el cubo con la esfera en la situación anterior

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

![Ejercicio 1](Img/FISICAS%20Ejercicio%201.gif)

### **Ejercicio 2**

En el siguiente ejercicio, primero he creado 3 Esferas a las cuales les he añadido un Rigidbody y el siguiente Script:

```C#
using UnityEngine;

[RequireComponent(typeof(Collider))]
[RequireComponent(typeof(Rigidbody))]
public class CollisionColorLogger : MonoBehaviour
{
    [Header("Color de colisión")]
    public bool usarColorAleatorio = true;
    public Color colorColision = Color.red;      // se usa si usarColorAleatorio = false

    [Header("Qué objetos cambiar")]
    public bool cambiarColorDelOtro = true;      // cambia el color del objeto con el que colisionas
    public bool cambiarMiColor = false;          // cambia también el color de este objeto

    void Reset()
    {
        // Asegurar configuración básica de Rigidbody para colisiones fiables
        var rb = GetComponent<Rigidbody>();
        rb.useGravity = true;
        rb.isKinematic = false;
        rb.interpolation = RigidbodyInterpolation.Interpolate;
        rb.collisionDetectionMode = CollisionDetectionMode.Continuous;
    }

    void OnCollisionEnter(Collision collision)
    {
        GameObject yo = gameObject;
        GameObject otro = collision.gameObject;

        Debug.Log($"[{yo.name}] colisionó con [{otro.name}]");

        // Elegir color
        Color c = usarColorAleatorio ? Random.ColorHSV(0f, 1f, 0.6f, 1f, 0.6f, 1f) : colorColision;

        // Cambiar color del otro objeto
        if (cambiarColorDelOtro)
        {
            if (otro.TryGetComponent<Renderer>(out var rendOtro))
            {
                rendOtro.material.color = c; // .material crea instancia del material sólo para ese objeto
            }
        }

        // Cambiar mi color
        if (cambiarMiColor)
        {
            if (TryGetComponent<Renderer>(out var miRenderer))
            {
                miRenderer.material.color = c;
            }
        }
    }
}

```

En el siguiente GIF se puede ver el funcionamiento descrito:

![Ejercicio 2](Img/FISICAS%20EJERCICIO%202.gif)

En el vídeo se puede apreciar como en la consola se muestran los mensajes cuando los objetos colisionan y cambian sus colores. Además de comprobarse visualmente como ocurre esto.

### **Ejercicio 3**

Para resolver el ejercicio, lo que hice fue crear los siguientes 3 Scripts:

- `PlayerStats.cs`: Guarda el daño del jugador

```C#
using UnityEngine;

public class PlayerStats : MonoBehaviour
{
    [Header("Estadísticas del jugador")]
    public int dano = 0;

    public void AddDamage(int amount)
    {
        dano += amount;
        Debug.Log($"[PlayerStats] Daño acumulado: {dano}");
    }
}

```

- `ColorZoneTrigger.cs`: Cambia el color al entrar y lo revierte al salir de una zona designada

```C#
using System.Collections.Generic;
using UnityEngine;

[RequireComponent(typeof(Collider))]
public class ColorZoneTrigger : MonoBehaviour
{
    [Header("Config")]
    public string playerTag = "Player";
    public Color colorAlEntrar = Color.green;

    // Guardamos el color original por Renderer para poder revertirlo al salir
    private readonly Dictionary<Renderer, Color> _coloresOriginales = new();

    void Reset()
    {
        var col = GetComponent<Collider>();
        col.isTrigger = true; // Esta zona funciona como Trigger
    }

    void OnTriggerEnter(Collider other)
    {
        if (!other.CompareTag(playerTag)) return;

        if (other.TryGetComponent<Renderer>(out var rend))
        {
            if (!_coloresOriginales.ContainsKey(rend))
            {
                _coloresOriginales[rend] = rend.material.color;
            }
            rend.material.color = colorAlEntrar;
            Debug.Log($"[ColorZoneTrigger] {other.name} entró → color {colorAlEntrar}");
        }
    }

    void OnTriggerExit(Collider other)
    {
        if (!other.CompareTag(playerTag)) return;

        if (other.TryGetComponent<Renderer>(out var rend))
        {
            if (_coloresOriginales.TryGetValue(rend, out var original))
            {
                rend.material.color = original;
                _coloresOriginales.Remove(rend);
                Debug.Log($"[ColorZoneTrigger] {other.name} salió → color restaurado");
            }
        }
    }
}

```

- `DamageZoneTrigger.cs`: Hace que una zona que incrementa el daño

```C#
using UnityEngine;

[RequireComponent(typeof(Collider))]
public class DamageZoneTrigger : MonoBehaviour
{
    [Header("Configuración")]
    public string playerTag = "Player";
    public int danoAlEntrar = 10;

    [Header("Daño continuo")]
    public bool danoContinuo = false;
    public int danoPorSegundo = 5;

    private PlayerStats playerStats;    // referencia al script del jugador
    private bool dentroZona = false;    // controla si el jugador está dentro

    void Reset()
    {
        var col = GetComponent<Collider>();
        col.isTrigger = true; // aseguramos que la zona sea Trigger
    }

    void OnTriggerEnter(Collider other)
    {
        if (!other.CompareTag(playerTag)) return;

        if (other.TryGetComponent<PlayerStats>(out var stats))
        {
            playerStats = stats;
            dentroZona = true;

            playerStats.AddDamage(danoAlEntrar);
            Debug.Log($"[DamageZoneTrigger] {other.name} entró → +{danoAlEntrar} daño | Total: {playerStats.dano}");
        }
    }

    void OnTriggerStay(Collider other)
    {
        if (!danoContinuo || playerStats == null) return;
        if (!other.CompareTag(playerTag)) return;

        int danoIncremental = Mathf.RoundToInt(danoPorSegundo * Time.deltaTime);
        if (danoIncremental > 0)
        {
            playerStats.AddDamage(danoIncremental);
            Debug.Log($"[DamageZoneTrigger] {other.name} dentro → +{danoIncremental} daño | Total: {playerStats.dano}");
        }
    }

    void OnTriggerExit(Collider other)
    {
        if (other.CompareTag(playerTag))
        {
            dentroZona = false;
            playerStats = null;
            Debug.Log($"[DamageZoneTrigger] {other.name} salió de la zona de daño");
        }
    }
}

```

Una vez creados estos Scripts, al tener el personaje creado, que en este caso es un Cubo blanco, de los ejercicios anteriores, lo que hice fue crear las dos zonas, que son dos cubos muy grandes y situé al personaje en el medio. Asigné a la zona de la derecha el Script `ColorZoneTrigger.cs`, que hace que el cubo blanco cambie al color verde y el cubo de la izquierda le asigné el Script `DamageZoneTrigger.cs`, haciendo que cuando el personaje entre en la zona, se muestre por consola el daño que tiene el personaje y cuanto ha aumentado, que siempre aumenta en 10 al tenerlo asignado así.
En el siguiente GIF se puede ver el funcionamiento de los Scripts, así como de las zonas y el personaje designado:

![Ejercicio 3](Img/FISICAS%20EJERCICIO%203.gif)

### **Ejercicio 4**

En este ejercicio, lo que hice fue crear las siguientes capas (Layers):

- Player

-  Enemy

- Collectible

Después asigné las capas de la siguiente forma: 

- El jugador → Layer: Player

- Todos los enemigos → Layer: Enemy.

- Todos los recolectables → Layer: Collectible.

Después configure la tabla `Layer Collision Matrix` de ola siguiente forma:

| Par de capas           | Estado |
|------------------------|:------:|
| Player ↔ Enemy         | ✅     |
| Enemy ↔ Enemy          | ❌     |
| Enemy ↔ Collectible    | ❌     |
| Player ↔ Collectible   | ✅     |

Configuré los elementos mencionados anteriormente de la siguiente forma:

- Jugador: Le añadí un Rigidbody

- Collider: Sin trigger

- Recolectables: Con trigger y le añadí el Script `CollectibleTrigger` a todos los recolectables

El Script comentado tiene el siguiente código:

```C#
using UnityEngine;

public class CollectibleTrigger : MonoBehaviour
{
    public string playerLayerName = "Player";
    int playerLayer;

    void Awake()
    {
        playerLayer = LayerMask.NameToLayer(playerLayerName);
    }

    void OnTriggerEnter(Collider other)
    {
        if (other.gameObject.layer == playerLayer)
        {
            Debug.Log($"[Collectible] Detectado jugador: {other.name} → {name}");
        }
    }
}

```

A continuación se puede apreciar un GIF en el que se ve como el jugador, en este caso un cubo blanco, puede colisionar con los enemigos, representados con objetos 3D de tipo `Capsule` de color negro. Además, se puede apreciar como al pasar el cubo por los coleccionables, que son esferas de color amarillo, aparece en la consola un mensaje que indica que se ha recolectado ese coleccionable

![Ejercicio 4](Img/FISICAS%20EJERCICIO%204.gif)

### **Ejercicio 5**

### Ejercicio: Materiales Físicos y AddForce()

Se crearon tres objetos con distintos `Physic Materials` para observar diferencias en fricción y rebote:

- **Resbaladizo:**  
  - *Dynamic Friction:* 0.1  
  - *Static Friction:* 0.1  
  - *Friction Combine:* Minimum  
  - *Bounciness:* 0.0  
  - *Bounce Combine:* Average  

- **Rugoso:**  
  - *Dynamic Friction:* 1.0  
  - *Static Friction:* 1.0  
  - *Friction Combine:* Maximum  
  - *Bounciness:* 0.0  
  - *Bounce Combine:* Average  

- **Rebote alto:**  
  - *Dynamic Friction:* 0.1  
  - *Static Friction:* 0.1  
  - *Friction Combine:* Multiply  
  - *Bounciness:* 1.0  
  - *Bounce Combine:* Maximum  

Mediante un script (`ForceLauncher`), al pulsar `X` se aplicó una fuerza (`AddForce()`) a cada objeto, comprobando cómo los materiales modifican su movimiento y reacción física sobre el plano.

```C#
using UnityEngine;

public class ForceLauncher : MonoBehaviour
{
    [Header("Objetos a lanzar (Rigidbodies)")]
    public Rigidbody[] bodies;

    [Header("Fuerza de lanzamiento")]
    public Vector3 direction = new Vector3(1f, 0.2f, 0f); // algo de componente Y para despegar del suelo
    public float magnitude = 8f;
    public ForceMode forceMode = ForceMode.Impulse; // impulso instantáneo

    void Update()
    {
        if (Input.GetKeyDown(KeyCode.X))
        {
            Vector3 force = direction.normalized * magnitude;

            foreach (var rb in bodies)
            {
                if (rb == null) continue;
                rb.velocity = Vector3.zero;        // reinicia velocidad para comparar reacciones
                rb.angularVelocity = Vector3.zero; // sin spin inicial
                rb.AddForce(force, forceMode);
            }

            Debug.Log($"[ForceLauncher] Lanzados {bodies.Length} objetos con fuerza {force} ({forceMode})");
        }
    }
}

```

En el siguiente GIF se puede apreciar el comportamiento de los distintos materiales. La posición de las esferas con respecto a sus materiales adjuntos es el siguiente:

- Izquierda → Material Rugoso

- Centro → Material con alto rebote

- Derecha → Material deslizante

![Ejercicio 5](Img/FISICAS%20Ejercicio%205.gif)


Como vemos, la esfera central rebota mucho más que las demás, la de la izquierda le cuesta moverse, mientras que la de la derecha del todo se mueve sin esfuerzo.
