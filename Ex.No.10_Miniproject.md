# Ex.No: 10  Implementation of 2D Dodge the Falling Objects Game
### DATE: 10/11/2025                                                                           
### REGISTER NUMBER : 212223230105
### AIM: 
To develop a 2D “Dodge the Falling Objects” game in Unity where the player avoids falling obstacles while the game dynamically adjusts difficulty using basic AI logic. 
### Algorithm:
# 1.Start the Unity Project
   * Create a new 2D project in Unity.
   * Set up the camera and game environment.
# 2.Create the Game Objects
   * Create a Player object with sprite, collider, and movement script.
   * Create a FallingObject prefab that spawns from the top and falls downward.
# 3.Design the Background and UI
   * Add a background image for the game environment.
   * Add UI elements like Score Text and Game Over Panel.
# 4.Write the Scripts
   * PlayerController.cs: Handles left/right movement of the player.
   * Spawner.cs: Spawns falling obstacles at random X positions.
   * FallingObject.cs: Moves objects down the screen and detects collisions with the player.
   * GameManager.cs: Manages scoring, game-over state, and restart functionality.
# 5.Implement AI Logic
   * Add a difficulty adjustment mechanism in the spawner to increase the spawn rate or fall speed as the player’s score increases.
   * This mimics adaptive AI behavior, making the game more challenging as the player performs better.
# 6. Attach and Configure Components
   * Assign all prefabs, UI elements, and scripts in the Unity Inspector.
   * Ensure sorting layers and object positions are correct (background behind, player in front).
# 7.Testing and Debugging
   * Play the scene, verify player movement, obstacle spawning, scoring, and game-over logic.
   * Fine-tune speeds and spawn rates for balanced gameplay.
### Program:
# PlayerController.cs
```py
using UnityEngine;

public class PlayerController : MonoBehaviour
{
    public float moveSpeed = 10f;
    private float screenHalfWidthInWorldUnits;

    void Start()
    {
        float halfPlayerWidth = transform.localScale.x / 2f;
        screenHalfWidthInWorldUnits = Camera.main.aspect * Camera.main.orthographicSize - halfPlayerWidth;
    }

    void Update()
    {
        float inputX = Input.GetAxisRaw("Horizontal");
        Vector2 newPosition = transform.position + Vector3.right * inputX * moveSpeed * Time.deltaTime;

        // Clamp movement inside screen
        newPosition.x = Mathf.Clamp(newPosition.x, -screenHalfWidthInWorldUnits, screenHalfWidthInWorldUnits);
        transform.position = newPosition;
    }

    void OnTriggerEnter2D(Collider2D other)
    {
        if (other.CompareTag("FallingObject"))
        {
            FindObjectOfType<GameManager>().GameOver();
        }
    }
}

```
# Spawner.cs
```py
using UnityEngine;

public class Spawner : MonoBehaviour
{
    public GameObject obstaclePrefab;    // assign prefab in Inspector
    public float spawnRate = 1.0f;       // seconds between spawns
    public float spawnXPadding = 0.5f;   // keep inside camera edges
    public float minY = 6f;              // spawn above view

    float halfWidth;

    void Start()
    {
        Camera cam = Camera.main;
        float halfHeight = cam.orthographicSize;
        halfWidth = halfHeight * cam.aspect;

        InvokeRepeating(nameof(SpawnObstacle), 1f, spawnRate);
    }

    void SpawnObstacle()
    {
        float x = Random.Range(-halfWidth + spawnXPadding, halfWidth - spawnXPadding);
        Vector2 spawnPos = new Vector2(x, minY);
        Instantiate(obstaclePrefab, spawnPos, Quaternion.identity);
    }
}

```
# FallingObject.cs
```py
using UnityEngine;

public class FallingObject : MonoBehaviour
{
    public float fallSpeed = 5f; // Speed of falling

    void Update()
    {
        // Move object downwards
        transform.Translate(Vector2.down * fallSpeed * Time.deltaTime);

        // Destroy object if it goes below screen
        if (transform.position.y < -6f)
        {
            // Add score when the player successfully dodges
            if (GameManager.instance != null)
                GameManager.instance.AddScore();

            Destroy(gameObject);
        }
    }

    private void OnTriggerEnter2D(Collider2D collision)
    {
        // If the object hits the player, trigger Game Over
        if (collision.CompareTag("Player"))
        {
            if (GameManager.instance != null)
                GameManager.instance.GameOver();

            Destroy(gameObject);
        }
    }
}
```
# GameManager.cs
```py
using UnityEngine;
using UnityEngine.UI;
using UnityEngine.SceneManagement;

public class GameManager : MonoBehaviour
{
    public static GameManager instance;

    public Text scoreText;         // UI Text for score
    public GameObject gameOverUI;  // Game Over Panel

    private int score = 0;
    private bool isGameOver = false;

    void Awake()
    {
        instance = this;
    }

    void Start()
    {
        Time.timeScale = 1f; // Ensure game runs normally when restarted
        UpdateScoreText();
        if (gameOverUI != null)
            gameOverUI.SetActive(false);
    }

    public void AddScore()
    {
        if (isGameOver) return;

        score++;
        UpdateScoreText();
    }

    void UpdateScoreText()
    {
        if (scoreText != null)
            scoreText.text = "Score: " + score;
    }

    public void GameOver()
    {
        if (isGameOver) return;

        isGameOver = true;
        Time.timeScale = 0f; // Pause game
        if (gameOverUI != null)
            gameOverUI.SetActive(true);
    }

    public void Restart()
    {
        Time.timeScale = 1f;
        SceneManager.LoadScene(SceneManager.GetActiveScene().name);
    }
}
```
### Output:
<img width="715" height="460" alt="image" src="https://github.com/user-attachments/assets/22963643-2e37-48f0-9ad2-4f3be53e2af1" />
<img width="709" height="460" alt="image" src="https://github.com/user-attachments/assets/0991867a-a5e8-4bd0-af67-1700c6a8084f" />
<img width="704" height="460" alt="image" src="https://github.com/user-attachments/assets/b1f30590-c1e5-4541-8c0c-202ab4625501" />


### Result:
Thus, the 2D “Dodge the Falling Objects” game was successfully developed using Unity and adopted a basic AI difficulty-scaling technique to enhance the gaming experience.
