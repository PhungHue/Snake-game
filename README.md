
#include<bits/stdc++.h>
#include<SDL.h>
using namespace std;
// do hoa
// lamf tach biet dau va than

const int width = 800;
const int hight = 600;
const int step = 20; // Kích thước của mỗi đốt con rắn
int u, v;
SDL_Window* window = NULL;
SDL_Renderer* renderer = NULL;
vector<SDL_Rect> snake; // Vector chứa các đốt của con rắn
SDL_Rect ball = {u, v, 20, 20};

void toado() {
    srand(time(0));
    u = rand() % (width - 20); // Tọa độ x ngẫu nhiên
    v = rand() % (hight - 20); // Tọa độ y ngẫu nhiên
    ball.x = u;
    ball.y = v;
    //thêm kiểm tra nếu bóng trùng với thân rn
}
//kiểm tra khi rắn quay đâuf
bool test_out2(char kitu)
{
    bool check=true;
    if(kitu=='r'&&snake[0].x<snake[1].x&&snake[0].y==snake[1].y) check=false;
    else if(kitu=='l'&&snake[0].x>snake[1].x&&snake[0].y==snake[1].y) check=false;
    else if(kitu=='u'&&snake[0].x==snake[1].x&&snake[0].y>snake[1].y) check=false;
    else if(kitu=='d'&&snake[0].x==snake[1].x&&snake[0].y<snake[1].y) check=false;
    return check;
}

//kiểm tra nếu rắn đâm vào thân
bool test_out3()
{
    bool check=true;
    int x=snake.size();
    for(int i=2;i<x;i++)
    {
        if(snake[0].x==snake[i].x&&snake[0].y==snake[i].y) check=false;
    }
    return check;
}

void velai(SDL_Rect ball) {
    //bong 1
    //tao bong
    SDL_SetRenderDrawColor(renderer, 0, 0, 0, 255);
    SDL_RenderClear(renderer);

    // Vẽ quả bóng
    SDL_SetRenderDrawColor(renderer, 255, 255, 0, 255);
    SDL_RenderFillRect(renderer, &ball);

    // Vẽ đầu con rắn
    SDL_SetRenderDrawColor(renderer, 100, 100 ,100 , 255);
    SDL_RenderFillRect(renderer, &snake[0]);


    //ve thân rắn
    SDL_SetRenderDrawColor(renderer, 255, 255,255 , 255);

    for(auto i=1;i<snake.size();i++)
    {
        SDL_RenderFillRect(renderer, &snake[i]);
    }

    SDL_RenderPresent(renderer);
}

void themdot() {
    // Tạo một đốt mới và thêm vào phía đầu con rắn
    SDL_Rect newSegment = {snake.front().x, snake.front().y, 20, 20};
    snake.insert(snake.begin(), newSegment);
}

int main(int argc, char* argv[]) {
    // mở sdl
    if (SDL_Init(SDL_INIT_VIDEO) < 0) {
        cout << "khong mo duoc thu vien sdl" << SDL_GetError() << endl;
        SDL_Quit();
    }
    // mở cửa sổ win
    window = SDL_CreateWindow("rắn siêu cùi bắp", SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED, width, hight, SDL_WINDOW_SHOWN);
    if (window == NULL) {
        cout << "khong mo duoc cua so win" << SDL_GetError() << endl;
        SDL_Quit();
    }
    // tạo bút vẽ
    renderer = SDL_CreateRenderer(window, -1, SDL_RENDERER_ACCELERATED);
    if (renderer == NULL) {
        cout << "khong tao duoc but ve" << SDL_GetError() << endl;
        SDL_DestroyWindow(window);
        SDL_Quit();
    }

    // bắt sự kiện ttuwf bàn phím
    SDL_Event e;
    bool quit = false;//rời sdl
    bool check1 = true;//kiểm tra nếu rán đâm vào tường
    char kitu = 'r';

    toado();
    snake.push_back({0, hight / 2 - 10, 20, 20}); // Thêm đốt ban đầu vào con rắn


        // Tạo đốt ban đầu cho con rắn
    snake.push_back({0, hight / 2 - 10, 20, 20});
    snake.push_back({0, hight / 2, 20, 20});
    snake.push_back({0, hight / 2 + 10, 20, 20});


    //ve qua bong
    SDL_SetRenderDrawColor(renderer, 255, 255, 255, 255);
    SDL_RenderFillRect(renderer, &ball);
    SDL_RenderPresent(renderer);
    // vòng lặp tắt cửa sổ
    while (!quit) {
        while (SDL_PollEvent(&e) != 0) {
            if (e.type == SDL_QUIT || (e.type == SDL_KEYDOWN && e.key.keysym.scancode == SDL_SCANCODE_Q)) {
                quit = true;
            }
            // xử lý sự kiện phím
            if (e.type == SDL_KEYDOWN) {
                switch (e.key.keysym.sym) {
                    case SDLK_LEFT:
                        kitu = 'l';
                        break;
                    case SDLK_RIGHT:
                        kitu = 'r';
                        break;
                    case SDLK_UP:
                        kitu = 'u';
                        break;
                    case SDLK_DOWN:
                        kitu = 'd';
                        break;
                }
                if (!check1) break;
            }
        }
        bool check2=test_out2(kitu);
        bool check3= test_out3();
        if (!check1||!check2||!check3) {
            cout << "game over!\n";
            break;
        }
        if (kitu == 'r') {
            if (snake.front().x >= width - 20) check1 = false;
            else {
                for (int i = snake.size() - 1; i > 0; --i) {
                    snake[i].x = snake[i - 1].x;
                    snake[i].y = snake[i - 1].y;
                }
                snake.front().x += step; // Di chuyển phần đầu của con rắn
            }
        }
        else if (kitu == 'l') {
            if (snake.front().x <= 0) check1 = false;
            else {
                for (int i = snake.size() - 1; i > 0; --i) {
                    snake[i].x = snake[i - 1].x;
                    snake[i].y = snake[i - 1].y;
                }
                snake.front().x -= step; // Di chuyển phần đầu của con rắn
            }
        }
        else if (kitu == 'u') {
            if (snake.front().y <= 0) check1 = false;
            else {
                for (int i = snake.size() - 1; i > 0; --i) {
                    snake[i].x = snake[i - 1].x;
                    snake[i].y = snake[i - 1].y;
                }
                snake.front().y -= step; // Di chuyển phần đầu của con rắn
            }
        }
        else {
            if (snake.front().y >= hight - 20) check1 = false;
            else {
                for (int i = snake.size() - 1; i > 0; --i) {
                    snake[i].x = snake[i - 1].x;
                    snake[i].y = snake[i - 1].y;
                }
                snake.front().y += step; // Di chuyển phần đầu của con rắn
            }
        }

        // Kiểm tra nếu con rắn ăn được quả bóng
        if ((snake.front().x >= ball.x - 20 && snake.front().x <= ball.x + 20) &&
            (snake.front().y >= ball.y - 20 && snake.front().y <= ball.y + 20)) {
            toado();
            themdot(); // Thêm một đốt mới vào con rắn
        }

        SDL_Delay(150);
        velai(ball); // Vẽ con rắn
    }

    // Giải phóng tài nguyên
    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);
    SDL_Quit();
    return 0;
}
