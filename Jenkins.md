## Jenkins [gitlab과 연동하기]



### 1. jenkins란?

- 프로젝트의 CI/CD를 도와주고 자동으로 빌드를 해주는 tool



### 2. Jenkins와 gitlab 연동하기

- AWS ec2를 실행 => docker 설치 => jenkins 설치 => gitlab 과 연동

  

1. AWS 회원가입 후 카드등록까지 완료

   

2. EC2 인스턴스 시작

   - 전부 다음!! 선택 => 중간에 용량 30GB설정 => 마지막에 암호키 발급받기

   - 발급받은 암호키(1)는 잘 보관하기

     

3. 탄력적IP 주소할당

   - 작업 탭에서 현재 running하는 인스턴스와 연결

   - 연결이 완료되면 고정된 퍼블릭 IPv4주소가 할당된다.

   - 고정된 퍼블릭 IPv4주소 == 어딘가에 존재하는 나의 AWS컴퓨터 IP주소

     

4. PUTTY 다운로드

   

5. PUTTYgen으로 암호키 생성하기

   - 보관해놓은 발급받은 암호키(1)로 또 다른 암호키(2)를 생성하고 같은 폴더에 저장해서 보관

     

6. PUTTY 설정 후 Open

   ![17](https://user-images.githubusercontent.com/73927750/150244255-192bd0df-90f3-469d-bc8d-098b271bcb0b.png)

   ![4](https://user-images.githubusercontent.com/73927750/150160376-d7cc6880-a207-4da5-9131-42330a3c868c.png)

   

7. 여기까지가 EC2 실행 완료 ==================> docker 설치

   

8. docker 설치

   ```shell
   $ sudo apt-get update # 업데이트
   $ sudo apt-get upgrade # 업그레이드
   
   $ curl -fsSL https://get.docker.com/ | sudo sh # 도커설치
   $ sudo usermod -aG docker $USER # cmd창에서 docker이 가능하도록 설정
   ```

   

9. jenkins 설치

   ```shell
   $ sudo docker pull jenkins/jenkins:lts # jenkins 최신버전으로 설치
   $ sudo docker run -d -p 8181:8080 -v /jenkins:/var/jenkins_home --name my_jenkins -u root jenkins/jenkins:lts # 8181 포트로 my_jenkins라는 이름을 가지는 jenkins 실행
   ```

10. jenkins 설치 확인

    ```shell
    $ sudo docker ps # 현재 실행중인 docker확인
    ```

    ![7](https://user-images.githubusercontent.com/73927750/150160777-f284e9e1-274e-4703-8562-a697611e5535.JPG)

    

11. http://(퍼블릭 IPv4):8181 주소로 들어가면 jenkins화면이 나오고 token값을 요구함

    

12. token 값 찾기

    ```shell
    $ sudo docker exec -it my_jenkins /bin/bash # jenkins 컨테이너로 들어가기
    `#` cd /var/jenkins_home/secrets # 해당 디렉토리 initialAdminPassword 파일이 존재(안에 token이 있음)
    `#` cat initialAdminPassword # token값 확인 => 복사해서 jenkins 페이지에 붙여넣기
    ```

    

13. 회원가입

    

14. 추천 plugins 설치 선택

    ![1](https://user-images.githubusercontent.com/73927750/150160572-3dda52f1-4578-4108-a4bc-cb88f8174dc5.JPG)

    

15. 여기까지가 docker로 만든 jenkins에 들어온 것임 ==================> gitlab연동

    

16. 플러그인 설치

    - Manage Jenkins => Manage Plugins => Available => SSH, gitlab 설치

      ![3](https://user-images.githubusercontent.com/73927750/150161794-9d0d8618-80da-45f7-9f78-d5db60b3b353.png)

      ![8](https://user-images.githubusercontent.com/73927750/150160936-8dbf12ed-ac70-478f-810c-7352fa4cb36f.png)

      

17. jenkins에서 작업을 위한 item생성

    - new item => 이름 선택 => Freestyle project

      ![9](https://user-images.githubusercontent.com/73927750/150160973-149a92e2-7563-4e08-a4a4-a42cdc7d19e2.png)

      

18. gitlab에서 jenkins에 트리거를 보내기 위해서는 jenkins의 secret token이 필요하고 url도 필요함

    - URL

      ![10](https://user-images.githubusercontent.com/73927750/150161129-827f3b4b-7ffc-436b-9075-7f8d07273fa1.png)

    - secret token

      ![11](https://user-images.githubusercontent.com/73927750/150161259-637ee16f-5f56-4386-90fe-aff679e5b160.png)

      ![12](https://user-images.githubusercontent.com/73927750/150161267-211a615a-982f-448b-a627-8fd1681332ec.png)

    - 각각 따로 저장 후에 SAVE

      

19. 저장한 URL, secret token을 설정하러 gitlab로 이동

    - 원하는 프로젝트에 들어가서 => settings => integrations => 아래 목록 중 Jenkins 선택

      ![13](https://user-images.githubusercontent.com/73927750/150161374-18171113-8c46-4e6a-96fb-a7c17b9f3a08.png)

      

20. webhooks 설정

    - Go to Webhooks 클릭 => URL, secret token입력

      ![14](https://user-images.githubusercontent.com/73927750/150161377-5e79961d-42fa-4129-a9db-8c35968aec1c.png)

      

21. 설정이 잘되었는지 확인

    ![15](https://user-images.githubusercontent.com/73927750/150161379-8d4c4396-8f5f-4c1d-8fae-f2b86d611661.png)

    ![2](https://user-images.githubusercontent.com/73927750/150161538-10c7ac66-1979-4577-9395-f176916491f5.JPG)

    

22. gitlab설정 완료 ==================> 다시 jenkins로 이동

    

23. 소스코드관리로 이동해서 Repository URL과 Credentials 설정

    - None과 Git 중에 Git 선택 => Repository URL에 git clone할때 https 주소 입력

    - Add를 클릭하고 Credentials 설정

      ![3](https://user-images.githubusercontent.com/73927750/150161861-d1e555a3-3edc-414a-8fb3-fda26eba4c22.JPG)

      

24. SAVE 끝~

    

25. 이후에는 gitlab에 push 할때마다 jenkins에서 감지하고 build한다

    - 해당 build history도 확인가능

      ![16](https://user-images.githubusercontent.com/73927750/150161944-b57b9aeb-8800-4d7e-ba96-36142020a9c0.png)