# RDR2.
// =======================
// GAME MANAGER
// =======================
using UnityEngine;
using UnityEngine.SceneManagement;

public class GameManager : MonoBehaviour
{
    void Start()
    {
        Time.timeScale = 1f;
        Application.targetFrameRate = 60;
    }

    public void Restart()
    {
        SceneManager.LoadScene(SceneManager.GetActiveScene().buildIndex);
    }
}

// =======================
// PLAYER MOVEMENT (MOBIL)
// =======================
using UnityEngine;

public class PlayerMovement : MonoBehaviour
{
    public float speed = 4f;
    public CharacterController controller;
    public Joystick joystick;
    float gravity = -9.8f;
    Vector3 velocity;

    void Update()
    {
        float x = joystick.Horizontal;
        float z = joystick.Vertical;

        Vector3 move = transform.right * x + transform.forward * z;
        controller.Move(move * speed * Time.deltaTime);

        velocity.y += gravity * Time.deltaTime;
        controller.Move(velocity * Time.deltaTime);
    }
}

// =======================
// CAMERA (THIRD PERSON)
// =======================
using UnityEngine;

public class ThirdPersonCamera : MonoBehaviour
{
    public Transform target;
    public Vector3 offset;
    public float smooth = 5f;

    void LateUpdate()
    {
        Vector3 pos = target.position + offset;
        transform.position = Vector3.Lerp(transform.position, pos, smooth * Time.deltaTime);
        transform.LookAt(target);
    }
}

// =======================
// PLAYER HEALTH
// =======================
using UnityEngine;
using UnityEngine.SceneManagement;

public class PlayerHealth : MonoBehaviour
{
    public float health = 100f;

    public void TakeDamage(float dmg)
    {
        health -= dmg;
        if (health <= 0)
            SceneManager.LoadScene(SceneManager.GetActiveScene().buildIndex);
    }
}

// =======================
// SHOOTING + RECOIL
// =======================
using UnityEngine;

public class PlayerShoot : MonoBehaviour
{
    public Camera cam;
    public float damage = 25f;
    public float range = 100f;
    public Recoil recoil;

    public void Shoot()
    {
        recoil.Fire();

        RaycastHit hit;
        if (Physics.Raycast(cam.transform.position, cam.transform.forward, out hit, range))
        {
            EnemyAI e = hit.transform.GetComponent<EnemyAI>();
            if (e) e.TakeDamage(damage);

            BossAI b = hit.transform.GetComponent<BossAI>();
            if (b) b.TakeDamage(damage);
        }
    }
}

using UnityEngine;

public class Recoil : MonoBehaviour
{
    Vector3 rot;
    public float kick = 2f;
    public float returnSpeed = 10f;

    void Update()
    {
        rot = Vector3.Lerp(rot, Vector3.zero, returnSpeed * Time.deltaTime);
        transform.localRotation = Quaternion.Euler(rot);
    }

    public void Fire()
    {
        rot += new Vector3(-kick, Random.Range(-1f, 1f), 0);
    }
}

// =======================
// ENEMY AI
// =======================
using UnityEngine;

public class EnemyAI : MonoBehaviour
{
    public float health = 100f;
    public float speed = 2f;
    public Transform player;
    public GameObject lootPrefab;

    void Update()
    {
        if (!player) return;
        transform.LookAt(player);
        transform.position += transform.forward * speed * Time.deltaTime;
    }

    public void TakeDamage(float dmg)
    {
        health -= dmg;
        if (health <= 0)
            Die();
    }

    void Die()
    {
        if (lootPrefab)
            Instantiate(lootPrefab, transform.position, Quaternion.identity);
        Destroy(gameObject);
    }
}

// =======================
// BOSS
// =======================
using UnityEngine;

public class BossAI : MonoBehaviour
{
    public float health = 300f;
    public Transform player;
    public float speed = 1.5f;

    void Update()
    {
        if (!player) return;
        transform.LookAt(player);
        transform.position += transform.forward * speed * Time.deltaTime;
    }

    public void TakeDamage(float dmg)
    {
        health -= dmg;
        if (health <= 0)
            FindObjectOfType<EndGameManager>().WinGame();
    }
}

// =======================
// LOOT + STATS
// =======================
using UnityEngine;

public class PlayerStats : MonoBehaviour
{
    public static int money = 0;
}

using UnityEngine;

public class Loot : MonoBehaviour
{
    public int value = 10;

    void OnTriggerEnter(Collider other)
    {
        if (other.CompareTag("Player"))
        {
            PlayerStats.money += value;
            Destroy(gameObject);
        }
    }
}

// =======================
// INVENTORY
// =======================
using UnityEngine;
using System.Collections.Generic;

public class Inventory : MonoBehaviour
{
    public static Inventory instance;
    public List<string> items = new List<string>();

    void Awake()
    {
        instance = this;
    }

    public void AddItem(string name)
    {
        items.Add(name);
    }
}

// =======================
// QUEST SYSTEM
// =======================
using UnityEngine;

public class QuestManager : MonoBehaviour
{
    public static QuestManager instance;
    public bool completed = false;

    void Awake()
    {
        instance = this;
    }

    public void CompleteQuest()
    {
        completed = true;
    }
}

// =======================
// SAVE SYSTEM
// =======================
using UnityEngine;

public class SaveSystem
{
    public static void Save()
    {
        PlayerPrefs.SetInt("money", PlayerStats.money);
    }

    public static void Load()
    {
        PlayerStats.money = PlayerPrefs.GetInt("money", 0);
    }
}

// =======================
// PAUSE
// =======================
using UnityEngine;

public class PauseMenu : MonoBehaviour
{
    public GameObject ui;
    bool paused;

    public void Toggle()
    {
        paused = !paused;
        ui.SetActive(paused);
        Time.timeScale = paused ? 0 : 1;
    }
}

// =======================
// MAIN MENU
// =======================
using UnityEngine;
using UnityEngine.SceneManagement;

public class MainMenu : MonoBehaviour
{
    public void StartGame()
    {
        SceneManager.LoadScene("Game");
    }

    public void Quit()
    {
        Application.Quit();
    }
}

// =======================
// END GAME
// =======================
using UnityEngine;
using UnityEngine.SceneManagement;

public class EndGameManager : MonoBehaviour
{
    public void WinGame()
    {
        SceneManager.LoadScene("EndScreen");
    }
}// =======================
// GAME MANAGER
// =======================
using UnityEngine;
using UnityEngine.SceneManagement;

public class GameManager : MonoBehaviour
{
    void Start()
    {
        Time.timeScale = 1f;
        Application.targetFrameRate = 60;
    }

    public void Restart()
    {
        SceneManager.LoadScene(SceneManager.GetActiveScene().buildIndex);
    }
}

// =======================
// PLAYER MOVEMENT (MOBIL)
// =======================
using UnityEngine;

public class PlayerMovement : MonoBehaviour
{
    public float speed = 4f;
    public CharacterController controller;
    public Joystick joystick;
    float gravity = -9.8f;
    Vector3 velocity;

    void Update()
    {
        float x = joystick.Horizontal;
        float z = joystick.Vertical;

        Vector3 move = transform.right * x + transform.forward * z;
        controller.Move(move * speed * Time.deltaTime);

        velocity.y += gravity * Time.deltaTime;
        controller.Move(velocity * Time.deltaTime);
    }
}

// =======================
// CAMERA (THIRD PERSON)
// =======================
using UnityEngine;

public class ThirdPersonCamera : MonoBehaviour
{
    public Transform target;
    public Vector3 offset;
    public float smooth = 5f;

    void LateUpdate()
    {
        Vector3 pos = target.position + offset;
        transform.position = Vector3.Lerp(transform.position, pos, smooth * Time.deltaTime);
        transform.LookAt(target);
    }
}

// =======================
// PLAYER HEALTH
// =======================
using UnityEngine;
using UnityEngine.SceneManagement;

public class PlayerHealth : MonoBehaviour
{
    public float health = 100f;

    public void TakeDamage(float dmg)
    {
        health -= dmg;
        if (health <= 0)
            SceneManager.LoadScene(SceneManager.GetActiveScene().buildIndex);
    }
}

// =======================
// SHOOTING + RECOIL
// =======================
using UnityEngine;

public class PlayerShoot : MonoBehaviour
{
    public Camera cam;
    public float damage = 25f;
    public float range = 100f;
    public Recoil recoil;

    public void Shoot()
    {
        recoil.Fire();

        RaycastHit hit;
        if (Physics.Raycast(cam.transform.position, cam.transform.forward, out hit, range))
        {
            EnemyAI e = hit.transform.GetComponent<EnemyAI>();
            if (e) e.TakeDamage(damage);

            BossAI b = hit.transform.GetComponent<BossAI>();
            if (b) b.TakeDamage(damage);
        }
    }
}

using UnityEngine;

public class Recoil : MonoBehaviour
{
    Vector3 rot;
    public float kick = 2f;
    public float returnSpeed = 10f;

    void Update()
    {
        rot = Vector3.Lerp(rot, Vector3.zero, returnSpeed * Time.deltaTime);
        transform.localRotation = Quaternion.Euler(rot);
    }

    public void Fire()
    {
        rot += new Vector3(-kick, Random.Range(-1f, 1f), 0);
    }
}

// =======================
// ENEMY AI
// =======================
using UnityEngine;

public class EnemyAI : MonoBehaviour
{
    public float health = 100f;
    public float speed = 2f;
    public Transform player;
    public GameObject lootPrefab;

    void Update()
    {
        if (!player) return;
        transform.LookAt(player);
        transform.position += transform.forward * speed * Time.deltaTime;
    }

    public void TakeDamage(float dmg)
    {
        health -= dmg;
        if (health <= 0)
            Die();
    }

    void Die()
    {
        if (lootPrefab)
            Instantiate(lootPrefab, transform.position, Quaternion.identity);
        Destroy(gameObject);
    }
}

// =======================
// BOSS
// =======================
using UnityEngine;

public class BossAI : MonoBehaviour
{
    public float health = 300f;
    public Transform player;
    public float speed = 1.5f;

    void Update()
    {
        if (!player) return;
        transform.LookAt(player);
        transform.position += transform.forward * speed * Time.deltaTime;
    }

    public void TakeDamage(float dmg)
    {
        health -= dmg;
        if (health <= 0)
            FindObjectOfType<EndGameManager>().WinGame();
    }
}

// =======================
// LOOT + STATS
// =======================
using UnityEngine;

public class PlayerStats : MonoBehaviour
{
    public static int money = 0;
}

using UnityEngine;

public class Loot : MonoBehaviour
{
    public int value = 10;

    void OnTriggerEnter(Collider other)
    {
        if (other.CompareTag("Player"))
        {
            PlayerStats.money += value;
            Destroy(gameObject);
        }
    }
}

// =======================
// INVENTORY
// =======================
using UnityEngine;
using System.Collections.Generic;

public class Inventory : MonoBehaviour
{
    public static Inventory instance;
    public List<string> items = new List<string>();

    void Awake()
    {
        instance = this;
    }

    public void AddItem(string name)
    {
        items.Add(name);
    }
}

// =======================
// QUEST SYSTEM
// =======================
using UnityEngine;

public class QuestManager : MonoBehaviour
{
    public static QuestManager instance;
    public bool completed = false;

    void Awake()
    {
        instance = this;
    }

    public void CompleteQuest()
    {
        completed = true;
    }
}

// =======================
// SAVE SYSTEM
// =======================
using UnityEngine;

public class SaveSystem
{
    public static void Save()
    {
        PlayerPrefs.SetInt("money", PlayerStats.money);
    }

    public static void Load()
    {
        PlayerStats.money = PlayerPrefs.GetInt("money", 0);
    }
}

// =======================
// PAUSE
// =======================
using UnityEngine;

public class PauseMenu : MonoBehaviour
{
    public GameObject ui;
    bool paused;

    public void Toggle()
    {
        paused = !paused;
        ui.SetActive(paused);
        Time.timeScale = paused ? 0 : 1;
    }
}

// =======================
// MAIN MENU
// =======================
using UnityEngine;
using UnityEngine.SceneManagement;

public class MainMenu : MonoBehaviour
{
    public void StartGame()
    {
        SceneManager.LoadScene("Game");
    }

    public void Quit()
    {
        Application.Quit();
    }
}

// =======================
// END GAME
// =======================
using UnityEngine;
using UnityEngine.SceneManagement;

public class EndGameManager : MonoBehaviour
{
    public void WinGame()
    {
        SceneManager.LoadScene("EndScreen");
    }
}
