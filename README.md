Here is a **beginner-friendly README** explaining how to build your **Flappy Bird-style game** in Unity from scratch.

---

# **Flappy Bird Clone - Unity Game**

This guide will walk you through creating a **Flappy Bird-style** game in **Unity** using C#. By the end, you'll have a fully functional game where a bird jumps through pipes, earns points, and restarts after hitting obstacles.

---

## 🎮 **Game Features**
✅ Tap to make the bird flap  
✅ Pipes spawn at random heights  
✅ Pipes move from right to left  
✅ Score increases when passing through pipes  
✅ Game over when the bird hits an obstacle  
✅ Restart button to play again  

---

## 🛠 **Requirements**
- **Unity** (2021 or later recommended)  
- **C# knowledge** (basic programming concepts)  
- **Basic understanding of Unity components like GameObjects, Rigidbody2D, and Colliders**  

---

## 🏗 **Step 1: Setting Up the Unity Project**

1. **Open Unity and Create a New 2D Project**  
   - Open Unity Hub → Click **New Project** → Select **2D Core**  
   - Name it **FlappyBirdClone**  
   - Click **Create**  

2. **Import the Assets**
   - Download and add the provided assets (`bird.png`, `pipe.png`, background).
   - Drag the images into the **Assets** folder in Unity.

---

## 🕊 **Step 2: Creating the Bird (Player)**

### **1. Add the Bird**
- **Right-click in the Hierarchy** → `Create Empty` → Rename it **Bird**
- **Drag `bird.png` from Assets to Hierarchy**  
- **Add a Rigidbody2D component**:  
  - In the Inspector, click **Add Component → Rigidbody2D**
  - Set **Gravity Scale = 1**  
  - Freeze Rotation (`Z`) to prevent flipping  

- **Add a Collider**:  
  - Click **Add Component → Circle Collider 2D**  
  - Adjust size if needed  

### **2. Attach the Bird Script**
Create a **BirdScript.cs** file inside `Assets/Scripts` and paste this:

```csharp
using UnityEngine;

public class BirdScript : MonoBehaviour
{
    public Rigidbody2D myRigidbody;
    public float flapStrength = 5f;
    public LogicScript logic;
    public bool birdIsAlive = true;

    void Start()
    {
        logic = GameObject.FindGameObjectWithTag("Logic").GetComponent<LogicScript>();
    }

    void Update()
    {
        if (Input.GetKeyDown(KeyCode.Space) && birdIsAlive)
        {
            myRigidbody.velocity = Vector2.up * flapStrength;
        }
    }

    private void OnCollisionEnter2D(Collision2D collision)
    {
        logic.gameOver();
        birdIsAlive = false;
    }
}
```
- **Assign `myRigidbody` in Inspector**  
- The bird jumps when you press **Space**  

---

## 🌉 **Step 3: Creating the Pipes**

### **1. Add the Pipe Prefab**
- Create a new **Empty GameObject** → Rename it **Pipe**  
- **Drag `pipe.png` onto the Pipe GameObject**  
- **Add a BoxCollider2D** to detect collisions  
- **Tag it as "Obstacle"**  

### **2. Pipe Movement Script**
Create a **PipeMoveScript.cs** file inside `Assets/Scripts`:

```csharp
using UnityEngine;

public class PipeMoveScript : MonoBehaviour
{
    public float moveSpeed = 5f;
    public float deadZone = -10f;

    void Update()
    {
        transform.position += Vector3.left * moveSpeed * Time.deltaTime;

        if (transform.position.x < deadZone)
        {
            Destroy(gameObject);
        }
    }
}
```
- **Attach this script to the Pipe Prefab**  
- Pipes move left & get deleted when off-screen  

---

## 🚀 **Step 4: Spawning Pipes**
To generate pipes at random heights:

### **1. Create the Pipe Spawner**
- Create an **Empty GameObject** → Rename it **PipeSpawner**
- Create a new script **PipeSpawnScript.cs** and paste this:

```csharp
using UnityEngine;

public class PipeSpawnScript : MonoBehaviour
{
    public GameObject pipePrefab;
    public float spawnRate = 2f;
    private float timer = 0;
    public float heightOffset = 2f;

    void Start()
    {
        SpawnPipe();
    }

    void Update()
    {
        if (timer < spawnRate)
        {
            timer += Time.deltaTime;
        }
        else
        {
            SpawnPipe();
            timer = 0;
        }
    }

    void SpawnPipe()
    {
        float lowestPoint = transform.position.y - heightOffset;
        float highestPoint = transform.position.y + heightOffset;
        Instantiate(pipePrefab, new Vector3(transform.position.x, Random.Range(lowestPoint, highestPoint), 0), Quaternion.identity);
    }
}
```
- **Assign the Pipe Prefab in Inspector**
- Pipes spawn at random heights every `2` seconds  

---

## 📊 **Step 5: Adding the Score System**
We need to **detect when the player passes pipes** and update the score.

### **1. Create the Logic Manager**
- Create an **Empty GameObject** → Rename it **LogicManager**
- Create a new script **LogicScript.cs** and paste this:

```csharp
using UnityEngine;
using UnityEngine.UI;
using UnityEngine.SceneManagement;

public class LogicScript : MonoBehaviour
{
    public int playerScore = 0;
    public Text scoreText;
    public GameObject gameOverScreen;

    public void addScore(int scoreToAdd)
    {
        playerScore += scoreToAdd;
        scoreText.text = playerScore.ToString();
    }

    public void restartGame()
    {
        SceneManager.LoadScene(SceneManager.GetActiveScene().name);
    }

    public void gameOver()
    {
        gameOverScreen.SetActive(true);
    }
}
```
- Attach this script to **LogicManager**  
- Create a **UI Text** and assign `scoreText` in Inspector  
- Add a **Game Over UI panel** & link it to `gameOverScreen`  

### **2. Detect Score Increase**
Create a new script **PipeMiddleScript.cs** and attach it to a **trigger collider** between the pipes:

```csharp
using UnityEngine;

public class PipeMiddleScript : MonoBehaviour
{
    public LogicScript logic;

    void Start()
    {
        logic = GameObject.FindGameObjectWithTag("Logic").GetComponent<LogicScript>();
    }

    private void OnTriggerEnter2D(Collider2D collision)
    {
        if (collision.gameObject.layer == 3)
        {
            logic.addScore(1);
        }
    }
}
```
- **This increases the score when the bird passes through pipes.**  

---

## 🎮 **Step 6: Adding Game Over & Restart**
- Go to **Canvas → UI Button**  
- Rename it **RestartButton**  
- Link it to `LogicScript.restartGame()`

---

## 🏁 **Final Steps**
✅ Assign the correct GameObjects in the Inspector  
✅ Make sure `BirdScript`, `PipeSpawnScript`, and `LogicScript` are attached properly  
✅ Click **Play** and start playing! 🎉

---

## 🛠 **Future Improvements**
- Add background music  
- Improve graphics (smooth animations)  
- Create different difficulty levels  

---

## 🎉 **Congratulations!**
You’ve built a **Flappy Bird Clone in Unity!** 🚀
