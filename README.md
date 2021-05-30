# first-unity-project

해상도는 1080x1920에서 제대로 작동하며
터치시 게임시작
장애물이 등장하며 주인공은 장애물사이를 통과해야합니다.
장애물 통과시 점수가 증가됩니다.
주인공의 목숨은 3개입니다.
주인공의 목숨을 다 소모하면 최고점수와 현재 점수를 출력합니다.
오른쪽 상단에 정지 버튼
정지 버튼 작동시 홈화면, 볼륨 조절, 이어하기 버튼이 구현 되어있습니다.


![image](https://user-images.githubusercontent.com/62591497/119225764-d4f41880-bb40-11eb-932f-2de3eaa800bf.png)

![image](https://user-images.githubusercontent.com/62591497/119225741-aa09c480-bb40-11eb-9c27-073c6da7dafa.png)

소스코드 



using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;
using UnityEngine.SceneManagement;

public class OneCommand : MonoBehaviour {

    public GameObject Column, Floor, White, Quit, countBird1, countBird2;
    public AudioSource PlayerSound_1, PlayerSound_2, PlayerSound_3, PlayerSound_4;
    public GameObject background1, background2, background3, background4, background5; //봄,여름, 가을, 겨울, 라스트 
    public GameObject floor1, floor2, floor3, floor4; //봄, 여름, 가을 ,겨울
    public Text Score;
    public GameObject preview;
    public GameObject heart1, heart2;
    public GameObject Restartbt;
    public GameObject sliderbt;
    public GameObject Volumebt;
    public GameObject startBt;
    public GameObject PauseButton;
    public Slider volume;
    
    Rigidbody2D rig;
    float NextTime = 0;
    int i, j, BestScore = 0;
    bool Stop = false;

    int count = 2;
    int test = 1;
    int flag = 0;
    int pausebt = 0;
    int Volumeflag = 0;

    GameObject[] gameObjects = new GameObject[3];
   

	void Start () {
        
        rig = GetComponent<Rigidbody2D>();
        rig.AddForce(Vector3.up * 270);
        PlayerSound_1.Play();

 
    }
    void CountDown()
    {
        
        if (count == 2)
        {
            
            heart2.SetActive(false);
            
        }
        else if(count == 1)
        {
            
            heart1.SetActive(false);

        }
        count--;

    }
    
    void Stage()
    {
        if(i > 10 && i<15) //여름
        {
            background1.SetActive(false); //봄->여름
            background2.SetActive(true);
            floor1.SetActive(false);
            floor2.SetActive(true);
            Floor = floor2;
        }
        else if (i > 15 && i<25) //가을
        {

            background2.SetActive(false); //봄->여름
            background3.SetActive(true);
            floor2.SetActive(false);
            floor3.SetActive(true);
            Floor = floor3;
        }
        else if (i > 25 && i <30) //겨울
        {
            background3.SetActive(false); //봄->여름
            background4.SetActive(true);
            floor3.SetActive(false);
            floor4.SetActive(true);
            Floor = floor4;
        }
        else if (i > 30) // 라스트 판
        {
            background4.SetActive(false); //봄->여름
            background5.SetActive(true);
            Floor.SetActive(false);

        }

    }




    void FixedUpdate()
    {
        Stage();
        Volume();
        // 뒤로가기
        if (Input.GetKeyDown(KeyCode.Escape)) Application.Quit();

        // 새
        GetComponent<Animator>().SetFloat("Velocity", rig.velocity.y);
        if (transform.position.y > 4.75f) transform.position = new Vector3(-1.5f, 4.75f, 0f);
        //토관이 한개인 경우 두개 이상인 경우


        if (transform.position.y < -2.55f)
        {

            Floor.GetComponent<Animator>().enabled = false;
            if (count > 0)
            {



                rig.simulated = false;
                flag = 1;


                if (gameObjects[1] == null && gameObjects[2] == null)
                {
                    rig.transform.position = new Vector3(-1.5f, 1, 0);
                }
                else if (j == 2 && gameObjects[0] != null && gameObjects[1] != null)
                {
                    Debug.Log(1);
                    rig.transform.position = new Vector3(gameObjects[0].transform.position.x - 0.5f, gameObjects[0].transform.position.y - 0.5f, 0);
                    Destroy(gameObjects[2]);
                }
                else if (j == 0 && gameObjects[1] != null && gameObjects[2] != null)
                {
                    Debug.Log(2);
                    rig.transform.position = new Vector3(gameObjects[1].transform.position.x - 0.5f, gameObjects[1].transform.position.y - 0.5f, 0);
                    Destroy(gameObjects[0]);
                }
                else if (j == 1 && gameObjects[0] != null && gameObjects[2] != null)
                {
                    rig.transform.position = new Vector3(gameObjects[2].transform.position.x - 0.5f, gameObjects[2].transform.position.y - 0.5f, 0);
                    Destroy(gameObjects[1]);
                }

                CountDown();
                rig.transform.position = new Vector3(gameObjects[2].transform.position.x - 0.5f, gameObjects[2].transform.position.y - 0.5f, 0);

            }
            else
            {
                GameOver();
            }
        }



        if (rig.velocity.y > 0) transform.rotation = Quaternion.Euler(0, 0, Mathf.Lerp(transform.rotation.z, 30f, rig.velocity.y / 8f));
        else transform.rotation = Quaternion.Euler(0, 0, Mathf.Lerp(transform.rotation.z, -90f, -rig.velocity.y / 8f));

        

        

        if (Stop) return;
        
        if ((Input.touchCount == 1 && Input.GetTouch(0).phase == TouchPhase.Began) || Input.GetMouseButtonDown(0) && test==1)
        {
            flag = 0;
            rig.simulated = true;
            rig.velocity = Vector3.zero;
            rig.AddForce(Vector3.up * 270);
            PlayerSound_1.Play();
            
        }

        // 기둥생성기
        if(flag != 1&& test==1)
        {
            if (Time.time > NextTime)
            {
                NextTime = Time.time + 1.7f;
                gameObjects[j] = (GameObject)Instantiate(Column, new Vector3(4, Random.Range(-1f, 3.2f), 0), Quaternion.identity);
                if (++j == 3) j = 0;
            }
            if (gameObjects[0])
            {
                gameObjects[0].transform.Translate(-0.03f, 0, 0);
                if (gameObjects[0].transform.position.x < -4) Destroy(gameObjects[0]);
            }
            if (gameObjects[1])
            {
                gameObjects[1].transform.Translate(-0.03f, 0, 0);
                if (gameObjects[1].transform.position.x < -4) Destroy(gameObjects[1]);
            }
            if (gameObjects[2])
            {
                gameObjects[2].transform.Translate(-0.03f, 0, 0);
                if (gameObjects[2].transform.position.x < -4) Destroy(gameObjects[2]);
            }
        }
    }
        
    
    
    

    void OnTriggerEnter2D(Collider2D col)
    {
        // 점수
        if (col.gameObject.name == "Column(Clone)")
        {
            Score.text = (++i).ToString();
            PlayerSound_2.Play();
        }
        else if (!Stop)
        {
            rig.velocity = Vector3.zero;
            PlayerSound_4.Play();
            GameOver();
        }
    }

    void GameOver()
    {
        // 게임오버
        if (!Stop) PlayerSound_3.Play();
        Floor.GetComponent<Animator>().enabled = false;
        White.SetActive(true);
        
        if (count == 0)
        {
            Stop = true;
            Score.gameObject.SetActive(false);
            if (PlayerPrefs.GetInt("BestScore", 0) < int.Parse(Score.text)) PlayerPrefs.SetInt("BestScore", int.Parse(Score.text));
            if (transform.position.y < -2.55f)
            {
                Quit.SetActive(true);
                Quit.transform.Find("ScoreScreen").GetComponent<Text>().text = Score.text;
                Quit.transform.Find("BestScreen").GetComponent<Text>().text = PlayerPrefs.GetInt("BestScore").ToString();
            }
            
        }
        else
        {
            
           
            if (count == 2)
            {

                heart2.SetActive(false);

            }
            else if (count == 1)
            {

                heart1.SetActive(false);

            }
            count--;

        } 
    }

    public void Restart()
    {
        // 재시작
        //Restartbt.SetActive(false);
        SceneManager.LoadScene(SceneManager.GetActiveScene().buildIndex);
    }


    
    public void Stopbt()
    {
        Restartbt.SetActive(true);
        Volumebt.SetActive(true);
        startBt.SetActive(true);
        PauseButton.SetActive(false);
        if (pausebt == 0)
        {
            test = 0;
            Time.timeScale = 0;
            pausebt = 1;
            flag = 1;
        }
        
    }

    public void StartBt()
    {
        Restartbt.SetActive(false);
        Volumebt.SetActive(false);
        sliderbt.SetActive(false);
        startBt.SetActive(false);
        PauseButton.SetActive(true);
        Time.timeScale = 1f;
        pausebt = 0;
        flag = 0;
        test = 1;

    }


    public void Volumbt()
    {
        
        if (Volumeflag == 0)
        {
            sliderbt.SetActive(true);
            Volumeflag = 1;
        }
        else
        {
            
            sliderbt.SetActive(false);
            Volumeflag = 0;
        }
    }


    public void ReStartbt()
    {
        SceneManager.LoadScene(SceneManager.GetActiveScene().buildIndex);
        Time.timeScale = 1f;
        pausebt = 0;
        flag = 0;
        Restartbt.SetActive(false);
       
    }

    public void Volume()
    {

        

        PlayerSound_1.volume = volume.value;

        PlayerSound_2.volume = volume.value;

        PlayerSound_3.volume = volume.value;

        PlayerSound_4.volume = volume.value;
    }
}


[변경된 부분.pptx](https://github.com/m8765000/first-unity-project/files/6566270/default.pptx)
