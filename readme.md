using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Move : MonoBehaviour
{
    Rigidbody2D rb2D;
    Vector3 velocity;
    public Animator dog;

    public float Speed = 5f;
    public float Jump = 3f;
    public bool dead;
    public bool atack1;
    public bool atack2;
    public int healt;
    float nextfire;

    void Start()
    {
        rb2D = GetComponent<Rigidbody2D>();
        dead = false;
        atack1 = false;
        atack2 = false;
        healt = 100;
    }

    void Update()
    {
        if (dead == true)
        {

        }
        else if (dead == false)
        {
            velocity = new Vector3(Input.GetAxis("Horizontal"), 0f);
            transform.position += Speed * velocity * Time.deltaTime;
            dog.SetFloat("speed", Mathf.Abs(Input.GetAxis("Horizontal")));

            if (Input.GetKeyDown(KeyCode.Space) && Mathf.Approximately(rb2D.velocity.y, 0f))
            {
                rb2D.AddForce(Vector3.up * Jump, ForceMode2D.Impulse);
                dog.SetTrigger("jump");
            }

            if (Input.GetAxisRaw("Horizontal") == 1)
            {
                transform.rotation = Quaternion.Euler(0f, 180f, 0f);
            }
            else if (Input.GetAxisRaw("Horizontal") == -1)
            {
                transform.rotation = Quaternion.Euler(0f, 0f, 0f);
            }

            Attack();
            Damage();
        }
    }

    void Attack()
    {
        if (Input.GetMouseButtonDown(0) && Input.GetAxis("Horizontal") == 0 && Mathf.Approximately(rb2D.velocity.y, 0f))
        {
            dog.SetTrigger("attack");
            dead = true;
            StartCoroutine(attack());
            dead = false;
        }
    }

    IEnumerator attack()
    {
        yield return new WaitForSeconds(0.18f);
    }

    private void OnTriggerEnter2D(Collider2D collision)
    {
        if (collision.gameObject.name == "EAtack1")
        {
            atack1 = true;
        }

        if (collision.gameObject.name == "EAtack2")
        {
            atack2 = true;
        }
    }

    private void OnTriggerExit2D(Collider2D collision)
    {
        if (collision.gameObject.name == "EAtack1")
        {
            atack1 = false;
        }

        if (collision.gameObject.name == "EAtack2")
        {
            atack2 = false;
        }
    }

    void Damage()
    {
        if (atack1 == true)
        {
            if (Time.time > nextfire)
            {
                healt -= 25;
                nextfire = Time.time + 1f;
            }
        }

        if (atack2 == true)
        {
            if (Time.time > nextfire)
            {
                healt -= 25;
                nextfire = Time.time + 1f;
            }
        }

        if (healt <= 0)
        {
            dead = true;
        }
    }
}
