#include <stdio.h> // 표준 입출력 작업을 위한 헤더파일
#include <time.h> // 지뢰찾기의 총 시간을 보여주기 위한 헤더파일(작업 시간)
#include <stdlib.h> // 랜덤으로 지뢰를 넣기 위한 헤더파일(여러 유틸리티 함수 작업)
#include <windows.h> // 윈도우 응용 프로그램을 만들기 위한 헤더파일
#include <conio.h> // 키 입력의 작업을 도와주기 위한 헤더파일

#define _CRT_SECURE_NO_WARNINGS // scanf의 경고를 해결하기 위한 매크로

#define true 1 // 1을 true로 정의
#define false 0 // 0을 false로 정의

#define FLAG 97 // 깃발을 세우기 위해 97을 FLAG로 정의 
#define LEFT 75 // 왼쪽으로 가기 위해 75를 LEFT로 정의 
#define RIGHT 77 // 오른쪽으로 가기 위해 77을 RIGHT로 정의 
#define UP 72 // 위로 가기 위해 72를 UP로 정의 
#define DOWN 80 // 밑으로 가기 위해 80을 DOWN로 정의 
#define SPACE 32 // 박스를 깨기 위해 32를 SPACE로 정의 

void initialization(char Map[][20], int size); // 2차원 배열을 0 초기화 시키기 위한 함수 선언(배열, 배열 크기);
void randMine(char Map[][20], int mine, int x[21], int y[21], int size); // 랜덤 지뢰 넣기, 지뢰 위치 저장하기 위한 함수 선언(배열, 지뢰갯수, 지뢰 행, 지뢰 열, 배열 크기)
int check(int i, int j, int size); //지뢰 주변 위치에 숫자를 넣기에 위치가 맞는지 틀린지 확인하는 함수 선언(지뢰 행, 지뢰 열, 배열 크기)
void mineSurroundNum(char Map[][20], int x[21], int y[21], int mine, int size); //지뢰 주변 숫자 넣기위한 함수 선언(배열, 지뢰 행, 지뢰 열, 지뢰갯수, 배열 크기)
void startCount(); // 게임 시작 카운트를 보여주는 함수 선언
void gotoxy(int x, int y); // 좌표를 자유 자제로 움직이기 위한 함수 선언

void mainMenu(); // 지뢰찾기의 첫 메인화면을 보여주는 함수 선언
void gameStart(); // 지뢰찾기를 시작했을 때 게임을 보여주는 함수 선언
void end(); // 지뢰찾기 게임을 종료하였을 때 보여주는 화면 함수 선언
void explanation(); // 게임 설명, 룰을 설명해주는 함수 선언

void initialization(char Map[][20], int size) {  // 배열 초기화 함수
    int i, j; // 반복을 위한 변수
    for (i = 0; i < size; i++) { // 정해진 size만큼 행 반복
        for (j = 0; j < size; j++) { // 정해진 size만큼 열 반복
            Map[i][j] = '0'; // 배열 모두 0으로 초기화
        }
    }
}

void randMine(char Map[][20], int mine, int x[21], int y[21], int size) { //랜덤 지뢰 넣기, 지뢰 위치 저장
    int landMine1; // 행의 지뢰 위치 저장
    int landMine2; // 열의 지뢰 위치 저장
    int landMineNumber = 0; // 지뢰를 저장하기 위해 지뢰만큼 반복을 돌려주는 변수

    srand(time(NULL)); // 현 시간에서 랜덤으로 지뢰위치 정함

    while (landMineNumber < mine) { // mine - 1 만큼 지뢰 위치 저장 반복, mine만큼 지뢰 넣기
        landMine1 = rand() % 19 + 1; // x좌표를 위해 19 + 1까지의 랜덤한 값 저장
        landMine2 = rand() % 19 + 1; // y좌표를 위해 19 + 1까지의 랜덤한 값 저장
        if (Map[landMine1][landMine2] != '*') { // 이미 있는 지뢰 위치는 제외
            Map[landMine1][landMine2] = '*'; // 지뢰가 없는 위치에 지뢰를 넣음
            x[landMineNumber] = landMine1; // 1차원 배열을 통해 모든 지뢰 행의 위치를 저장
            y[landMineNumber] = landMine2; // 1차원 배열을 통해 모든 지뢰 열의 위치를 저장
        }
        landMineNumber++;
    }
}

int check(int i, int j, int size) { //지뢰 주변의 숫자들이 배열 크기를 넘어가는지 아닌지 판단하는 함수
    if (i >= 0 && i < size && j >= 0 && j < size)
        return true; // 주변 숫자가 배열 0이상 && size안에 들어가면 숫자를 넣어줌
    else
        return false; // 그렇지 않으면 숫자를 넣지 않음
}

void mineSurroundNum(char Map[][20], int x[21], int y[21], int mine, int size) { // 지뢰 주변 숫자 넣는 함수

    int leq_x, riq_y; // 지뢰의 왼쪽 상단 (행, 열)
    int lew_x, riw_y; // 지뢰의 상단 
    int lee_x, rie_y; // 지뢰의 오른쪽 상단  
    int lea_x, ria_y; // 지뢰의 왼쪽
    int led_x, rid_y; // 지뢰의 오른쪽
    int lez_x, riz_y; // 지뢰의 왼쪽 하단
    int lex_x, rix_y; // 지뢰의 하단
    int lec_x, ric_y; // 지뢰의 오른쪽 하단

    for (int i = 0; i < mine; i++) { // 지뢰 갯수만큼 반복
        // 모든 지뢰의 열과 행 위치를 변수에 저장
        leq_x = x[i] - 1;  riq_y = y[i] - 1;  // 왼쪽 상단
        lew_x = x[i] - 1;  riw_y = y[i];      // 상단
        lee_x = x[i] - 1;  rie_y = y[i] + 1;  // 오른쪽 상단

        lea_x = x[i];      ria_y = y[i] - 1;  // 왼쪽
        led_x = x[i];      rid_y = y[i] + 1;  // 오른쪽

        lez_x = x[i] + 1;  riz_y = y[i] - 1;  // 왼쪽 하단
        lex_x = x[i] + 1;  rix_y = y[i];      // 하단
        lec_x = x[i] + 1;  ric_y = y[i] + 1;  // 오른쪽 하단

        if (check(leq_x, riq_y, size)) {   // 왼쪽 상단 위치 체크를 위해 위치 검사 함수 사용
            if (Map[leq_x][riq_y] != '*')  // 왼쪽 상단에 지뢰가 없다면
                Map[leq_x][riq_y] += 1;    // +1을 한 값 저장
        }
        if (check(lew_x, riw_y, size)) {   // 상단 위치 체크를 위해 위치 검사 함수 사용
            if (Map[lew_x][riw_y] != '*')  // 상단에 지뢰가 없다면
                Map[lew_x][riw_y] += 1;    // +1을 한 값 저장
        }
        if (check(lee_x, rie_y, size)) {   // 오른쪽 상단 위치 체크를 위해 위치 검사 함수 사용
            if (Map[lee_x][rie_y] != '*')  // 오른쪽 상단에 지뢰가 없다면
                Map[lee_x][rie_y] += 1;    // +1을 한 값 저장
        }
        if (check(lea_x, ria_y, size)) {   // 왼쪽 위치 체크를 위해 위치 검사 함수 사용
            if (Map[lea_x][ria_y] != '*')  // 왼쪽에 지뢰가 없다면
                Map[lea_x][ria_y] += 1;    // +1을 한 값 저장
        }
        if (check(led_x, rid_y, size)) {   // 오른쪽 위치 체크를 위해 위치 검사 함수 사용
            if (Map[led_x][rid_y] != '*')  // 오른쪽 지뢰가 없다면
                Map[led_x][rid_y] += 1;    // +1을 한 값 저장
        }
        if (check(lez_x, riz_y, size)) {   // 왼쪽 하단 위치 체크를 위해 위치 검사 함수 사용
            if (Map[lez_x][riz_y] != '*')  // 왼쪽 하단에 지뢰가 없다면
                Map[lez_x][riz_y] += 1;    // +1을 한 값 저장
        }
        if (check(lex_x, rix_y, size)) {   // 하단 위치 체크를 위해 위치 검사 함수 사용
            if (Map[lex_x][rix_y] != '*')  // 하단에 지뢰가 없다면
                Map[lex_x][rix_y] += 1;    // +1을 한 값 저장
        }
        if (check(lec_x, ric_y, size)) {   // 오른쪽 하단 위치 체크를 위해 위치 검사 함수 사용
            if (Map[lec_x][ric_y] != '*')  // 오른쪽 하단에 지뢰가 없다면
                Map[lec_x][ric_y] += 1;    // +1을 한 값 저장
        }
    }
}

int main() { // 메인 함수
    system("mode con cols=53 lines=23"); // 콘솔 창 조절 cols = 가로, lines = 세로
                                         // 가로 53 세로 23으로 콘솔 창 조절
    system("tiTle ★ 지뢰찾기 게임 ★"); // 콘솔 창 제목 설정

    int choice; // 화면 이동을 위한 변수 선언

    while (1) { // while문 반복
        mainMenu(); // 메인메뉴를 첫 화면으로 출력
        fflush(stdin); // 이전 입력 버퍼에 저장된 값을 지움
        scanf_s("%d", &choice); // 메인 메뉴에서 원하는 화면 입력

        if (choice == 1) { // 1을 누를 시 
            system("cls"); // 화면 지움
            startCount(); // 게임 시작 전 카운트 화면으로 이동
        }
        else if (choice == 2) { // 2를 누를 시
            system("cls"); // 화면 지움
            explanation(); // 게임 룰, 게임 설명에 대한 화면으로 이동
        }
        else { // 이 외에 숫자를 입력 시
            system("cls"); // 화면 지움
            end(); // 게임 종료 화면 화면으로 이동
            break;
        }
    }
}

void mainMenu() { // 지뢰찾기의 첫 메인화면
    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 11); // 연한 옥색
    puts("□▣□▣□▣□▣□▣□▣□▣□▣□▣□▣□▣□▣□▣");
    puts("▣                                                □");
    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 6); // 노랑색
    puts("□      ─┲─  ※ ──┐ │  ─╇─  │  ──┐ ※             ▣");
    puts("▣       /※ │  ┌─┘ ※  /※ ├    / │              □");
    puts("□      /  ※│  └───│  /  ※│   /  │              ▣");
    puts("▣           │ ※└──┤    ─┬─       │              □");
    puts("□                        /※                     ▣");
    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 11); // 연한 옥색
    puts("□                                                ▣");
    puts("▣                                                □");
    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 14); // 연한 노랑색
    puts("□          1. 게임 시작                          ▣");
    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 11); // 연한 옥색
    puts("▣                                                □");
    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 14); // 연한 노랑색
    puts("□          2. 게임 설명                          ▣");
    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 11); // 연한 옥색
    puts("▣                                                □");
    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 14); // 연한 노랑색
    puts("□          3. 종료                               ▣");
    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 11); // 연한 옥색
    puts("▣                                                □");
    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 7); // 흰색
    puts("□    || 대구가톨릭대학교 20117013 김유진 ||      ▣");
    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 11); //연한 옥색
    puts("▣                                                □");
    puts("□▣□▣□▣□▣□▣□▣□▣□▣□▣□▣□▣□▣□▣");
    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 6); // 노랑색
    puts("");
    printf("select : ");
}

void gameStart() {
    system("mode con cols=43 lines=20"); // 가로 43 세로 20으로 콘솔 창 조절
    clock_t starttime = clock(); // 게임 시작 시간 저장

    char Map[20][20]; // 2차원 배열 크기 변수
    int a[21], b[21]; // 지뢰 행, 열 위치 저장 변수

    initialization(Map, 20); // 배열 초기화 하기 위한 함수 
    randMine(Map, 20, a, b, 20); // 지뢰넣기, 지뢰 위치 저장 함수
    mineSurroundNum(Map, a, b, 20, 20); // 지뢰 주변 숫자 넣는 함수 

    char szChoice = 0; // 입력한 키를 저장하기 위한 변수
    int color = 1; // 네모박스 색을 랜덤으로 출력하기 위한 변수(0은 검정색이기 때문에 보이지 않아 1부터 시작함)
    int choice; // 게임이 종료되고 다시 할지 안할지 정하기 위한 변수
    int i, j; // 네모박스 출력을 위한 열, 행 변수

    for (i = 0; i < 20; i++) { // 행을 20만큼 반복
        for (j = 0; j < 20; j++) { // 열을 20만큼 반복
            SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), color); // 네모박스 색깔 입히기
            if (color >= 1 && color != 15) { // 네모박스 색이 1 ~ 15사이이면 
                color += 1; // 1씩 증가 시켜 색을 채움
            }
            else if (color == 15) { // 네모박스 색이 15이면 (15이상부터는 글자의 배경 색까지 칠하기 때문에 1부터 칠하도록 바꿈) 
                color = 1; // 다시 색을 1부터 시작함
            }
            printf("■"); // 숫자를 숨기기 위한 네모박스
        }
        printf("\n");
    }

    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 15); // 지뢰, 숫자 진한 흰색으로

    int x = 0, y = 0; // 좌표를 움직이기 위한 변수

    // 게임 승리, 깃발 잘못 세움를 보여주기 위한 변수
    int mineFLAG_true = 0; // 깃발을 지뢰위에 제대로 세운 갯수
    int mineFLAG_fail = 0; // 깃발을 잘못 새운 갯수
    int mineFLAG_sum = 0; // 깃발의 총 갯수

    int colsMax = 39; // 네모박스가 2칸씩 채웠기 때문에 배열 가로 (20 * 2) - 1을 한 값 (숫자는 1칸씩 차지하지만 네모박스는 2칸씩 차지하기 때문)
    int rowsMax = 19; // 네모박스 세로의 길이

    for (i = 0; i < 20; i++) { // 배열 안에 지뢰가 총 몇개 있는지 저장하기 위한 반복문
        for (j = 0; j < 20; j++) {  // 배열 크기만큼 반복함
            if (Map[i][j] == '*') { // 지뢰를 찾으면
                mineFLAG_sum += 1; // mineFLAG_sum 변수에 총 갯수 저장함
            }
        }
    }

    while (1) {
        gotoxy(x, y);
        if (_kbhit()) // 키보드가 눌렷는지 확인하는 _kbhit 함수 사용
        {
            szChoice = _getch();// 키보드가 입력되고 출력은 하지 않는 _getch() 함수 사용
            switch (szChoice) { // 키보드가 누른 입력 값을 확인하는 switch문
            case UP: // 위로가는 키를 눌럿을 시
                if (y >= 1 && y <= rowsMax) { // 네모박스의 세로 길이 안에 있으면
                    y--; // 위로 한칸씩 이동
                }
                break;
            case  LEFT: // 왼쪽으로 가는 키를 눌럿을 시
                if (x >= 2 && x < colsMax) { // 네모박스의 가로 길이 안에 있으면
                    x -= 2; // 왼쪽으로 2칸씩 이동
                }
                break;
            case DOWN: // 밑으로 가는 키를 눌럿을 시
                if (y >= 0 && y <= rowsMax - 1) { // 네모박스의 세로 길이 안에 있으면
                    y++; // 밑으로 한칸씩 이동
                }
                break;
            case RIGHT: // 오른쪽으로 가는 키를 눌럿을 시
                if (x >= 0 && x <= colsMax - 2) { // 네모박스의 가로 길이 안에 있으면
                    x += 2; // 오른쪽으로 2칸씩 이동
                }
                break;
            case FLAG: // 깃발을 세우기 위해 a를 눌럿을 시
                if (Map[(x / 2)][y] == '*') { // 지뢰 위치에 깃발을 세운다면
                    printf("  "); // 네모박스를 없애고
                    gotoxy(x, y); // 그 위치로 이동하여
                    Map[x / 2][y] = '?'; // 네모박스의 크기 때문에 가로의 길이를 나누기 2하여 그 배열에 깃발을 저장

                    printf("%c", Map[x / 2][y]); // 저장한 깃발을 출력
                    mineFLAG_true += 1; // 깃발을 제대로 세울 시 mineFLAG_true 변수에 1 저장
                    mineFLAG_fail += 1; // 모든 깃발의 세운 것을 확인 하기 위해 mineFLAG_fail 변수에도 1 저장
                    if (mineFLAG_true == mineFLAG_sum) { // 저장한 깃발이 모두 지뢰위치에 세웠다면 성공한 화면 출력
                        system("mode con cols=42 lines=35"); // 가로 42 세로 35로 콘솔 창 조절
                        clock_t endtime = clock(); // 게임 종료 시간 저장
                        puts("");
                        SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 14); // 텍스트 색 연한 노랑색으로 변경
                        gotoxy(0, 21); // 지뢰를 모두 출력하고 세로 21의 위치에 성공 표시
                        puts("□▣□▣□▣□▣□▣□▣□▣□"); // 지뢰찾기 성공 화면 출력
                        puts("□                          □");
                        puts("▣   ♬ 지뢰찾기 성공 ♬    ▣");
                        puts("□                          □");
                        puts("▣      1. 처음으로         ▣");
                        puts("□                          □");
                        puts("▣      2. 다시하기         ▣");
                        puts("□                          □");
                        puts("▣      3. 종료하기         ▣");
                        puts("□                          □");
                        puts("□▣□▣□▣□▣□▣□▣□▣□");
                        SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 6); // 텍스트 색 노랑색으로 변경
                        printf("Total Time: %d\n", (int)(endtime - starttime) / CLOCKS_PER_SEC); // 지뢰찾기를 수행한 총 시간 출력
                        puts("");
                        SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 14); // 텍스트 색 연한 노랑색으로 변경
                        printf("select : ");
                        scanf_s("%d", &choice); // 다시할지 안할 지 입력
                        if (choice == 1) { // choice가 1이면
                            system("cls"); // 화면 지움
                            main(); // 메인 화면으로 이동
                        }
                        else if (choice == 2) { // choice가 2이면
                            system("cls"); // 화면 지움
                            gameStart(); // 게임 화면을 처음으로 다시 돌려서 시작
                        }
                        else { // 이 외에 숫자 입력 시
                            system("cls"); // 화면 지움
                            end(); // 게임 종료 화면으로 이동
                            break;
                        }
                        system("cls"); // 화면 지움
                    }
                    break;
                }
                else { // 지뢰 위치가 아닌 다른 곳에 깃발을 세울 시
                    printf("  "); // 네모박스를 지움
                    gotoxy(x, y); // 그 위치로 이동
                    Map[x / 2][y] = '?'; // 배열에 깃발을 저장
                    printf("%c", Map[x / 2][y]); // 저장한 깃발을 출력
                    mineFLAG_fail += 1; // 잘못된 깃발을 세울 시 mineFLAG_fail 변수가 1증가
                    if (mineFLAG_fail == mineFLAG_sum) { // 잘못된 깃발의 갯수가 전체 깃발의 갯수와 같아질 시
                        system("mode con cols=35 lines=15"); // 가로 35 세로 15로 콘솔 창 조절
                        system("cls"); // 화면 지움
                        _sleep(2000); // 2초간 일시정지
                        SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 14); // 글자 색 연한 노랑색
                        puts("□▣□▣□▣□▣□▣□▣□▣□"); // 지뢰찾기 실패 화면 표시
                        puts("□                          □");
                        puts("▣    지뢰찾기 실패 -_┳     ▣");
                        puts("□                          □");
                        puts("▣      1. 처음으로         ▣");
                        puts("□                          □");
                        puts("▣      2. 다시하기         ▣");
                        puts("□                          □");
                        puts("▣      3. 종료하기         ▣");
                        puts("□                          □");
                        puts("□▣□▣□▣□▣□▣□▣□▣□");
                        puts("");
                        SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 6); // 글자 색 노랑색
                        printf("select : ");
                        scanf_s("%d", &choice); // 화면에 나오는 숫자 입력
                        if (choice == 1) { // 1을 누를 시
                            system("cls"); // 화면 지움
                            main(); // 메인 화면으로 이동
                        }
                        else if (choice == 2) { // 2를 누를 시
                            system("cls"); // 화면 지움
                            gameStart(); // 게임 첫 화면으로 이동
                        }
                        else { // 이 외에 숫자를 누를 시
                            system("cls"); // 화면 지움
                            end(); // 게임 종료 화면을 출력
                            break;
                        }
                        system("cls");
                    }
                    break;
                }

            case SPACE: // 스페이스를 누를 시
                if (Map[(x / 2)][y] == '*') { // 스페이스를 누른 곳에 지뢰가 있다면
                    printf("  "); // 네모박스 지움
                    gotoxy(x, y); // 그 위치로 이동

                    printf("%c", Map[x / 2][y]); // 그 위치에 있는 지뢰 출력
                    _sleep(2000); // 2초간 일시정지
                    system("cls"); // 화면지움
                    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 14); // 글자 색 노랑색
                    system("mode con cols=35 lines=15"); // 가로 35 세로 15로 콘솔 창 조절
                    puts("□▣□▣□▣□▣□▣□▣□▣□");
                    puts("□                          □");
                    puts("▣  ※ 지뢰를 밟았습니다※  ▣");
                    puts("□                          □");
                    puts("▣      1. 처음으로         ▣");
                    puts("□                          □");
                    puts("▣      2. 다시하기         ▣");
                    puts("□                          □");
                    puts("▣      3. 종료하기         ▣");
                    puts("□                          □");
                    puts("□▣□▣□▣□▣□▣□▣□▣□");
                    puts("");
                    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 6); // 노란색
                    printf("select : ");
                    scanf_s("%d", &choice); // 화면에 있는 숫자 입력 
                    if (choice == 1) { // 1을 누를 시
                        system("cls"); //화면 지움
                        main(); // 메인 화면으로 이동
                    }
                    else if (choice == 2) { // 2를 누를 시
                        system("cls"); // 화면 지움
                        gameStart(); // 게임 첫 화면으로 이동
                    }
                    else { // 이 외에 숫자를 누를 시
                        system("cls"); // 화면 지움
                        end(); // 게임 종료 화면으로 이동
                        break;
                    }
                    system("cls");
                }
                else { // 스페이스를 누른 곳에 지뢰가 없다면
                    printf("  "); // 네모박스 지움
                    gotoxy(x, y); // 그 위치로 이동

                    printf("%c", Map[x / 2][y]); // 그 위치에 있는 숫자를 출력 
                }
                break;
            }
        }
    }
}

void gotoxy(int x, int y) { // 좌표 이동을 위한 함수
    COORD pos = { x,y };
    SetConsoleCursorPosition(GetStdHandle(STD_OUTPUT_HANDLE), pos);
}

void end() { // 게임 종료를 할 시 출력할 함수
    system("mode con cols=53 lines=23"); // 가로 53 세로 23으로 콘솔 창 조절
    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 11);
    puts("□▣□▣□▣□▣□▣□▣□▣□▣□▣□▣□▣□▣□▣");
    puts("▣                                                □");
    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 6);
    puts("□      ─┲─  ※ ──┐ │  ─╇─  │  ──┐ ※             ▣");
    puts("▣       /※ │  ┌─┘ ※  /※ ├    / │              □");
    puts("□      /  ※│  └───│  /  ※│   /  │              ▣");
    puts("▣           │ ※└──┤    ─┬─       │              □");
    puts("□                        /※                     ▣");
    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 11);
    puts("□                                                ▣");
    puts("▣                                                □");
    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 14);
    puts("□          1. 게임 시작                          ▣");
    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 11);
    puts("▣                                                □");
    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 14);
    puts("□          2. 게임 설명                          ▣");
    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 11);
    puts("▣                                                □");
    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 14);
    puts("□          3. 종료                               ▣");
    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 11);
    puts("▣                                                □");
    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 7);
    puts("□    || 대구가톨릭대학교 20117013 김유진 ||      ▣");
    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 11);
    puts("▣                                                □");
    puts("□▣□▣□▣□▣□▣□▣□▣□▣□▣□▣□▣□▣□▣");
    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 6);
    puts("");
    puts("게임이 종료되었습니다.");
    exit(1); //프로그램 종료 함수. 헤더파일 : stdlib.h
}

void explanation() { // 게임 설명하기 위한 함수
    system("mode con cols=55 lines=27"); // 가로 55 세로 27로 콘솔 창 조절
    int choice; // 화면 이동을 위한 변수

    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 11); // 연한 옥색
    puts("□▣□▣□▣□▣□▣□▣□▣□▣□▣□▣□▣□▣□▣□");
    puts("▣                                                  ▣");
    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 6); // 노랑색
    puts("□          ★ 지뢰 찾기 게임 설명 ★               □");
    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 11); // 연한 옥색
    puts("▣                                                  ▣");
    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 14); // 연한 노랑색
    puts("□    ☞ Space를 누르면 네모 박스를 없앨 수 있음    □");
    puts("▣    ☞ 0이 나올 시 주변에 지뢰가 없다는 것        ▣");
    puts("□    ☞ 0이외에 숫자가 나올 시 주변에              □");
    puts("▣       그 숫자만큼 지뢰가 있음                    ▣");
    puts("□    ☞ * 은 지뢰 모양                             □");
    puts("▣    ☞ 모든 지뢰에 깃발을 세우면 성공!            ▣");
    puts("□    ☞ 지뢰가 아닌 곳에 깃발을 세울 시 실패!      □");
    puts("▣       지뢰에 Space를 누를 시 실패!               □");
    puts("□                                                  ▣");
    puts("□    ☞ 방향키(키보드)                             □");
    puts("▣       UP   : ▲    DOWN  : ▼                    ▣");
    puts("□       LEFT : ◀    RIGHT : ▶                    □");
    puts("▣                                                  ▣");
    puts("□    ☞ 깃발세우기(키보드) : a                     □");
    puts("□                깃발 모양 : ?                     □");
    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 11); // 연한 옥색
    puts("▣                                                  ▣");
    puts("□      1. 처음으로                                 □");
    puts("▣      2. 종료                                     ▣");
    puts("□                                                  □");
    puts("▣□▣□▣□▣□▣□▣□▣□▣□▣□▣□▣□▣□▣□▣");

    puts("");
    printf("select : ");
    scanf_s("%d", &choice); // 화면 이동을 위해 값 입력
    while (1) { // 맞을 때까지 반복
        if (choice == 1) { // choice가 1이면
            system("cls"); // 화면 지움
            main(); // 메인 화면으로 이동
        }
        else { // choice가 1이 아니면
            system("cls"); // 화면 지움
            end(); // 게임 종료화면으로 이동
            break;
        }
        system("cls");
    }
}

void startCount() {
    system("mode con cols=32 lines=13"); // 가로 32 세로 13으로 콘솔 창 조절
    int count = 3; // 카운트를 위해 count변수에 3을 저장
    int color = 6; // 초마다 색을 변경하기 위해 color변수에 6저장
    while (1) { // 맞을 때까지 반복
        system("cls"); // 화면 초기화
        if (count != 0) { // 0초가 될때까지 반복하는 조건문
            SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), color);
            puts("□▣□▣□▣□▣□▣□▣□▣□");
            puts("□                          □");
            puts("▣                          ▣");
            puts("□                          □");
            SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 14);
            printf("▣     %d초 후 시작합니다.   ▣\n", count--);
            SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), color);
            puts("□                          □");
            puts("▣                          ▣");
            puts("□                          □");
            puts("▣                          ▣");
            puts("□                          □");
            puts("□▣□▣□▣□▣□▣□▣□▣□");
            color++; // 1초마다 화면색 바꿔줌
            _sleep(1000); // 화면을 1초동안 일시정지
        }
        if (count == 0) { // 0초가 되면
            system("cls"); // 화면을 지움
            gameStart(); // 게임 화면으로 이동함
            break;
        }
    }
}