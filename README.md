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

![슬라이드1](https://user-images.githubusercontent.com/62591497/120106206-29c60d80-c197-11eb-8d54-33f8624be6c5.PNG)
![슬라이드2](https://user-images.githubusercontent.com/62591497/120106211-2cc0fe00-c197-11eb-8c41-e891fbb35b2f.PNG)
![슬라이드3](https://user-images.githubusercontent.com/62591497/120106218-33e80c00-c197-11eb-9b63-0d070a062035.PNG)
![슬라이드4](https://user-images.githubusercontent.com/62591497/120106219-3480a280-c197-11eb-8556-1fc499f2aece.PNG)
![슬라이드5](https://user-images.githubusercontent.com/62591497/120106220-3480a280-c197-11eb-9c3e-76dd32a6a59c.PNG)
![슬라이드6](https://user-images.githubusercontent.com/62591497/120106221-35193900-c197-11eb-9755-4f95c6db562b.PNG)
![슬라이드7](https://user-images.githubusercontent.com/62591497/120106222-35193900-c197-11eb-8ae4-b176da9095ed.PNG)
![슬라이드8](https://user-images.githubusercontent.com/62591497/120106237-49f5cc80-c197-11eb-9074-bc7cee45efb8.PNG)
![슬라이드9](https://user-images.githubusercontent.com/62591497/120106239-49f5cc80-c197-11eb-89fe-d27c2d1a9568.PNG)
![슬라이드10](https://user-images.githubusercontent.com/62591497/120106240-4a8e6300-c197-11eb-9fa7-b992c3277e94.PNG)
![슬라이드11](https://user-images.githubusercontent.com/62591497/120106241-4a8e6300-c197-11eb-99de-321d96f9a11d.PNG)
![슬라이드12](https://user-images.githubusercontent.com/62591497/120106242-4b26f980-c197-11eb-9b40-b06dfce04991.PNG)
![슬라이드13](https://user-images.githubusercontent.com/62591497/120106243-4b26f980-c197-11eb-9e48-28c39f02706f.PNG)
![슬라이드14](https://user-images.githubusercontent.com/62591497/120106244-4bbf9000-c197-11eb-8bc7-4f1edb1cc343.PNG)
![슬라이드15](https://user-images.githubusercontent.com/62591497/120106245-4bbf9000-c197-11eb-8825-cdc6bd590ee0.PNG)
![슬라이드16](https://user-images.githubusercontent.com/62591497/120106247-4c582680-c197-11eb-86ae-b24a3df06b41.PNG)
![슬라이드17](https://user-images.githubusercontent.com/62591497/120106248-4c582680-c197-11eb-9ef7-36589e992976.PNG)
![슬라이드18](https://user-images.githubusercontent.com/62591497/120106250-4cf0bd00-c197-11eb-9131-8854439de1a1.PNG)
![슬라이드19](https://user-images.githubusercontent.com/62591497/120106251-4cf0bd00-c197-11eb-8919-99c4935213ce.PNG)
![슬라이드20](https://user-images.githubusercontent.com/62591497/120106252-4d895380-c197-11eb-94a3-4330d31bf36a.PNG)
![슬라이드21](https://user-images.githubusercontent.com/62591497/120106253-4d895380-c197-11eb-84fc-f46ee3db3cf7.PNG)
![슬라이드22](https://user-images.githubusercontent.com/62591497/120106254-4e21ea00-c197-11eb-9324-f242166fc308.PNG)
![슬라이드23](https://user-images.githubusercontent.com/62591497/120106255-4e21ea00-c197-11eb-961d-5965bb177110.PNG)
![슬라이드24](https://user-images.githubusercontent.com/62591497/120106258-4eba8080-c197-11eb-8bef-1bca2041cd19.PNG)
![슬라이드25](https://user-images.githubusercontent.com/62591497/120106259-4eba8080-c197-11eb-9e92-aff4aa30800e.PNG)
![슬라이드26](https://user-images.githubusercontent.com/62591497/120106260-4f531700-c197-11eb-8609-dbe86e0cffad.PNG)
![슬라이드27](https://user-images.githubusercontent.com/62591497/120106261-4f531700-c197-11eb-8193-251e936c93b9.PNG)
![슬라이드28](https://user-images.githubusercontent.com/62591497/120106262-4febad80-c197-11eb-9c89-5150c2d5a214.PNG)
![슬라이드29](https://user-images.githubusercontent.com/62591497/120106264-4febad80-c197-11eb-9150-b3829baa6c12.PNG)
![슬라이드30](https://user-images.githubusercontent.com/62591497/120106265-50844400-c197-11eb-8528-08c975036343.PNG)
![슬라이드31](https://user-images.githubusercontent.com/62591497/120106266-50844400-c197-11eb-896a-3a7a5c72628a.PNG)
![슬라이드32](https://user-images.githubusercontent.com/62591497/120106236-495d3600-c197-11eb-9bca-b34d8d357a62.PNG)

