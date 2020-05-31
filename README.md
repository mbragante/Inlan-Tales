# Código do jogo Inlan Tales


Velocidade:

﻿using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class AddVeloc : MonoBehaviour
{
    private void OnTriggerEnter(Collider other)
    {
        if (other.tag == "hero")
        {
            Destroy(gameObject);
        }
    }
}


Vida:

﻿using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class AddVida : MonoBehaviour
{

  
    private void OnTriggerEnter(Collider other)
    {
        if (other.tag=="hero"||other.tag=="AstralMode")
        {
            Destroy(gameObject);
        }
    }
}


Astral:
﻿using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;
using UnityEngine.SceneManagement;
public class Astro : MonoBehaviour
{  //movimento
    
   
 

    //Movimento
   
    public Animator animator;
    public Rigidbody rb;
    Vector3 moviment;
    public bool nochao=true;
    public float veloc = 7;
    private Vector3 moveDirec;
    //Ataque
    public int semclick = 0;
    float ultimoclick = 0;
    public float intervalo_combo;
    public Slider baViFi;
    float tempoModo;
    float tempoAtual;
    //tempo
    public Text temTex;
    public float segundos;
    public Slider baviF;
 



    // Start is called before the first frame update
    void Start()
    {
        animator = GetComponent<Animator>();
        baViFi = GameObject.FindGameObjectWithTag("BarradeVidaAstral").GetComponent<Slider>();
        temTex = GameObject.FindGameObjectWithTag("Tempo").GetComponent<Text>();
        temTex.text = segundos.ToString();
        segundos = 15;
        baviF=GameObject.FindGameObjectWithTag("BarraDeVida").GetComponent<Slider>();


    }

    // Update is called once per frame
    void Update()
    {
        segundos -= Time.deltaTime;
        temTex.text = Mathf.Round(segundos).ToString();
        movimento();
        
        troca();
        if (Time.time - ultimoclick > intervalo_combo)
        {
            semclick = 0;
        }
        if (Input.GetMouseButtonDown(0))
        {
            ultimoclick = Time.time;
            semclick++;

            if (semclick == 1)
            {
                animator.SetBool("Ataque1", true);
            }
            semclick = Mathf.Clamp(semclick, 0, 3);
        }

        dash();
        if (baViFi.value <= 0|| baviF.value <= 0)
        {
            animator.SetBool("morrer1", true);
            StartCoroutine("morreu");
        }
        

    }
    void FixedUpdate()
    {
        if (Input.GetKeyDown(KeyCode.Space) && nochao)
        {
            rb.AddForce(new Vector3(0, 17, 0), ForceMode.Impulse);
            nochao = false;
            animator.SetBool("Pular1", true);

        }
        else
        {
            animator.SetBool("Pular1", false);
        }
    }
 
    public void troca()
    {

        if (Input.GetKeyDown(KeyCode.Tab)||segundos<=0)
        {
         
                Destroy(gameObject);
                GameObject.Find("Warrior").GetComponent<Player>().enabled = true;
                GameObject.Find("Warrior").GetComponent<Animator>().SetBool("Voltei", true);
       
         
            
        }
        else
        {
            GameObject.Find("Warrior").GetComponent<Animator>().SetBool("Voltei", false);
        }
    }
    IEnumerator morreu()
    {
        yield return new WaitForSeconds(3.0f);
        SceneManager.LoadScene("Lose");
    }
    public void return1()
    {
        if (semclick >= 2)
        {
            animator.SetBool("Ataque2", true);
        }
        else
        {
            animator.SetBool("Ataque1", false);
            semclick = 0;
        }
    }
    public void return2()
    {
        if (semclick >= 3)
        {
            animator.SetBool("Ataque3", true);
        }
        else
        {
            animator.SetBool("Ataque2", false);
            animator.SetBool("Ataque1", false);
            semclick = 0;
        }
    }
    public void return3()
    {
        if (semclick >= 3)
        {
            animator.SetBool("Ataque4", true);
        }
        else
        {
            animator.SetBool("Ataque1", false);
            animator.SetBool("Ataque2", false);
            animator.SetBool("Ataque3", false);
            semclick = 0;
        }
    }

    public void return4()
    {
  
           animator.SetBool("Ataque1", false);
           animator.SetBool("Ataque2", false);
           animator.SetBool("Ataque3", false);
           animator.SetBool("Ataque4", false);
        semclick = 0;
       
    }
   


    void movimento()
    {

        float x = Input.GetAxisRaw("Horizontal");
        float z = Input.GetAxisRaw("Vertical");
        Vector3 moviment = new Vector3(x, 0, z) * veloc * Time.deltaTime;
        rb.MovePosition(transform.position + moviment);
        if (x!=0||z!=0)
        {
            animator.SetBool("Andar1", true);
        }
        else
        {
            animator.SetBool("Andar1", false);
        }
        if (x == 1)
        {
            transform.rotation = Quaternion.Slerp(transform.rotation, Quaternion.Euler(0, 90, 0), Time.deltaTime * 5);
        }
        if (x == -1)
        {
            transform.rotation = Quaternion.Slerp(transform.rotation, Quaternion.Euler(0, -90, 0), Time.deltaTime * 5);
        }
        if (z == 1)
        {
            transform.rotation = Quaternion.Slerp(transform.rotation, Quaternion.Euler(0, 0 , 0), Time.deltaTime * 5);
        }
        if (z == -1)
        {
            transform.rotation = Quaternion.Slerp(transform.rotation, Quaternion.Euler(0, -180, 0), Time.deltaTime * 5);
        }
        //Pulo
     
    }
    private void dash()
    {
        if (Input.GetKeyDown(KeyCode.LeftShift))
        {
           rb.AddForce(transform.forward * 15f, ForceMode.VelocityChange);
           rb.velocity = Vector3.zero;
        }
    }

    private void OnTriggerEnter(Collider other)
    {

        if (other.gameObject.tag == "ItemVida")
        {
            baViFi.value += 3f;
        }
        if (other.gameObject.tag == "ArmaInimigo")
        {
            baViFi.value -= .2f;
        }
        if (other.gameObject.tag == "Terreno")
        {
            nochao = true;
        }
        if (other.tag == "ItemVeloc")
        {
            veloc += 1;
        }
    }
    private void OnCollisionStay(Collision collision)
    {
        nochao = true;
    }
    
   
}


Câmeras:

﻿using System.Collections;
﻿using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Cameras : MonoBehaviour
{
    public bool bloqCursor;
    public float sensib_mouse;
    public Transform alvo1;
    public float distancia_alvo;
    public Vector2 pitchMinMax = new Vector2(-40, 85);
    public float rotacTempo;
    Vector3 rotacaoVeloc;
    Vector3 atualRota;
    float yaw;
    float pitch;
  void Start()
    {
        if (bloqCursor)
        {
            Cursor.lockState =CursorLockMode.Locked;
            Cursor.visible = true;
        }
    }
   void LateUpdate()
    {
        yaw += Input.GetAxis("Mouse X") * sensib_mouse;
        pitch -= Input.GetAxis("Mouse Y") * sensib_mouse;
        pitch = Mathf.Clamp(pitch, pitchMinMax.x, pitchMinMax.y);
        atualRota = Vector3.SmoothDamp(atualRota, new Vector3(pitch, yaw), ref rotacaoVeloc, rotacTempo);
        transform.position = alvo1.position - transform.forward * distancia_alvo;
    }
}


Script do Inimigo:

﻿using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;
public class InimigoScript : MonoBehaviour
{

    public float veloc;
    public Transform modoFisico;
    public Transform modoAstral;
    int vida ;
    public Animator animav;
    public Rigidbody rbIni;
    float minDist=3f;
    float maxDist = 5;
    public GameObject lvida;
    GameObject fisico;
    GameObject astral;

    // Start is called before the first frame update
    void Start()
    {

        vida = 60;
        fisico = GameObject.FindGameObjectWithTag("hero");
       
    }

    // Update is called once per frame
    void Update()
    {

     
        transform.LookAt(modoFisico);

            if (Vector3.Distance(transform.position, modoFisico.position) >= minDist)
            {

                transform.position += transform.forward * veloc * Time.deltaTime;
                animav.SetBool("Andar", true);

                if (Vector3.Distance(transform.position, modoFisico.position) <= maxDist)
                {
                    animav.SetBool("Atacar", true);
                    animav.SetBool("Andar", false);
                }
                else
                {
                    animav.SetBool("Atacar", false);
                }
            }
        
       
        if (vida <= 0)
        {
            StartCoroutine("iniMorte");
            animav.SetBool("Morrer", true);
        }
    }

    IEnumerator iniMorte ()
    {
        yield return new WaitForSeconds(2.0f);
        Destroy(gameObject);
        Instantiate(lvida, transform.position + (transform.forward *-4.5f), transform.rotation);
    }

    private void OnTriggerEnter(Collider other)
    {
        if (other.tag == "ArmaAstral")
        {
            vida -= 4;
        }
        if (other.tag == "Arma")
        {
            vida -= 2;
        }

    }
   
}


Level:

﻿using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;
using UnityEngine.SceneManagement;
public class Levels : MonoBehaviour
{

    public void ingame()
    {
        SceneManager.LoadScene("Game");
    }
    public void sair()
    {
        Application.Quit();
    }
    public void menu(){
        SceneManager.LoadScene("Menu");
    }
}



Jogador:

﻿using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;
using UnityEngine.SceneManagement;
public class Player : MonoBehaviour
{  //movimento
    
   
 

    //Movimento
   
    public Animator anima;
    public Rigidbody rb;
    Vector3 moviment;
    public bool nochao=true;
    public float veloc = 7;
    private Vector3 moveDirec;
    //Ataque
    public int semclick = 0;
    float ultimoclick = 0;
    public float intervalo_combo;
    //troca
    public GameObject fastral;

    //dash
    private float velocDash;
    private float tempoDash;
    public float inicioDash;
    private int direction;
    private float forcaDash;
    public Slider baVi;


    // Start is called before the first frame update
    void Start()
    {
        anima = GetComponent<Animator>();
        
    }

    // Update is called once per frame
    void Update()
    {
        movimento();
        troca();
        if (Time.time - ultimoclick > intervalo_combo)
        {
            semclick = 0;
        }
        if (Input.GetMouseButtonDown(0))
        {
            ultimoclick = Time.time;
            semclick++;

            if (semclick == 1)
            {
                anima.SetBool("Attack1", true);
            }
            semclick = Mathf.Clamp(semclick, 0, 3);
        }
        if (baVi.value <= 0)
        {
            anima.SetBool("morrer", true);
           StartCoroutine("morreuScene");
        }

        dash();
  
    }
    void FixedUpdate()
    {
        if (Input.GetKeyDown(KeyCode.Space) && nochao)
        {
            rb.AddForce(new Vector3(0, 17, 0), ForceMode.Impulse);
            nochao = false;
            anima.SetBool("Pular", true);

        }
        else
        {
            anima.SetBool("Pular", false);
        }
    }
 
    void troca()
    {
        if (Input.GetKeyDown(KeyCode.Tab))
        {
            anima.SetBool("morrer", true);
            Instantiate(fastral, transform.position + (transform.forward * 2.5f), transform.rotation);
           GetComponent<Player>().enabled=false;
          
        }
        else
        {
            anima.SetBool("morrer", false);
            
        }
    }
    IEnumerator morreuScene()
    {
        yield return new WaitForSeconds(3.0f);
        SceneManager.LoadScene("Lose");
    }
    public void return1()
    {
        if (semclick >= 2)
        {
            anima.SetBool("Attack2", true);
        }
        else
        {
            anima.SetBool("Attack1", false);
            semclick = 0;
        }
    }
    public void return2()
    {
        if (semclick >= 3)
        {
            anima.SetBool("Attack3", true);
        }
        else
        {
            anima.SetBool("Attack2", false);
            anima.SetBool("Attack1", false);
            semclick = 0;
        }
    }

    public void return3()
    {
        anima.SetBool("Attack1", false);
        anima.SetBool("Attack2", false);
        anima.SetBool("Attack3", false);
        semclick = 0;
    }
    

    void movimento()
    {

        float x = Input.GetAxisRaw("Horizontal");
        float z = Input.GetAxisRaw("Vertical");
        Vector3 moviment = new Vector3(x, 0, z) * veloc * Time.deltaTime;
        rb.MovePosition(transform.position + moviment);
        if (x!=0||z!=0)
        {
            anima.SetBool("Andar", true);
        }
        else
        {
            anima.SetBool("Andar", false);
        }
        if (x == 1)
        {
            transform.rotation = Quaternion.Slerp(transform.rotation, Quaternion.Euler(0, 90, 0), Time.deltaTime * 5);
        }
        if (x == -1)
        {
            transform.rotation = Quaternion.Slerp(transform.rotation, Quaternion.Euler(0, -90, 0), Time.deltaTime * 5);
        }
        if (z == 1)
        {
            transform.rotation = Quaternion.Slerp(transform.rotation, Quaternion.Euler(0, 0 , 0), Time.deltaTime * 5);
        }
        if (z == -1)
        {
            transform.rotation = Quaternion.Slerp(transform.rotation, Quaternion.Euler(0, -180, 0), Time.deltaTime * 5);
        }
        //Pulo
     
    }
    private void dash()
    {
        if (Input.GetKeyDown(KeyCode.LeftShift))
        {
             rb.AddForce(transform.forward * 20f, ForceMode.VelocityChange);
             rb.velocity = Vector3.zero;
           

        }
    }

    private void OnTriggerEnter(Collider other)
    {

        if (other.gameObject.tag == "ItemVida")
        {
            baVi.value += 3f;
        }
        if (other.gameObject.tag == "ArmaInimigo")
        {
            baVi.value -= .4f;
        }
        if (other.gameObject.tag == "Terreno")
        {
            nochao = true;
        }
        if (other.tag == "ItemVeloc")
        {
            veloc += 1;
        }
    }
    private void OnCollisionStay(Collision collision)
    {
        nochao = true;
    }
    
   
}


Vitória:

﻿using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.SceneManagement;

public class Venceu : MonoBehaviour
{

    private void Start()
    {
   
    }
    private void OnTriggerEnter(Collider other)
    { if(other.tag=="hero"|| other.tag == "AstralMode")
        SceneManager.LoadScene("Venceu");
    }
}
