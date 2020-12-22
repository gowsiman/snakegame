#include<iostream>
#include<windows.h>
#include<time.h>
#include<conio.h>
using namespace std;

const int height=20;
const int length=50;
int snake_xpos=length/2;
int snake_ypos=height/2;
int food_ypos,food_xpos;
char playarea[height][length];
char direction;
int snake_len=1;
int x_pos[length*2];
int y_pos[length*2];
bool cond;
int score=0;

void create_playarea();
void display();
void create_food();
void start_game();
bool game_over();
void shift();
void input();
bool check_d(char,char);
int check_p();
void instructions();

int main()
{
    srand(time(0));
    create_playarea();
    display();
    instructions();
    input();
    start_game();
}

void create_playarea()
{
    for(int i=0;i<height;i++)
    {
        for(int j=0;j<length;j++)
        {
            if(i==0 || i==height-1)
            {
                playarea[i][j]='-';
            }
            else if(j==0 || j==length-1)
            {
                playarea[i][j]='|';
            }
            else
            {
                playarea[i][j]=' ';
            }
        }
    }
    playarea[snake_ypos][snake_xpos]='O';
    x_pos[0]=snake_xpos;
    y_pos[0]=snake_ypos;
    create_food();
}


void display()
{
    system("cls");
    for(int i=0;i<height;i++)
    {
        for(int j=0;j<length;j++)
        {
            cout<<playarea[i][j];
        }
        cout<<endl;
    }
}

void create_food()
{
    do
    {
        food_ypos=rand()%height;
        food_xpos=rand()%length;
    }while(food_ypos<1 || food_ypos>=height-1 || food_xpos<1 || food_xpos>=length-1 || check_p()==1);
    playarea[food_ypos][food_xpos]='*';
}

void start_game()
{
    do
    {
        cond=false;
        Sleep(25);
        shift();
        if(kbhit())
        {
            char input=getch();
            if(snake_len>1)
                direction=(check_d(direction,input))?input:direction;
            else    
                direction=input;
        }
    cout<<"\n\n_________________Your score is : "<<score<<"_________________"<<endl;
    }while(!game_over());
    cout<<"\n\n-----------------GAME OVER----------------------\n\n";
}

bool game_over()
{
    if(x_pos[0]==0 || x_pos[0]==length-1 || y_pos[0]==0 || y_pos[0]==height-1)
    {
        return true;
    }
    else if(check_p()==-1)
    {
        return true;
    }
    else
    {
        return false;
    }
}

void shift()
{
    int y_pre1=y_pos[0];
    int x_pre1=x_pos[0];
    
    switch(direction)
    {
        case 'a': x_pre1--;
                  break;
        
        case 'd': x_pre1++;
                  break;
        
        case 'w': y_pre1--;
                  break;
        
        case 's': y_pre1++;
                  break;
        
        default : x_pre1++;
                  break;
    }

    for(int i=0;i<snake_len;i++)
    {
        if(y_pre1==food_ypos && x_pre1==food_xpos)
        {
            cond=true;
            create_food();
            score+=10;
        }
        playarea[y_pre1][x_pre1]=playarea[y_pos[i]][x_pos[i]];
        playarea[y_pos[i]][x_pos[i]]=' ';
        swap(y_pos[i],y_pre1);
        swap(x_pos[i],x_pre1);
    }

    if(cond)
    {
        playarea[y_pre1][x_pre1]='o';
        y_pos[snake_len]=y_pre1;
        x_pos[snake_len]=x_pre1;
        snake_len++;
    }
    display();
}

int check_p()
{
    if(x_pos[0]==food_xpos && y_pos[0]==food_ypos)
        return 1;
    for(int i=1;i<snake_len;i++)
    {
        if(x_pos[i]==x_pos[0] && y_pos[i]==y_pos[0])
        {
            return -1;
        }
        else if(x_pos[i]==food_xpos && y_pos[i]==food_ypos)
        {
            return 1;
        }
    }
    return 0;
}

void input()
{
    cout<<"-----------Press any key to start-----------"<<endl;
    cin>> direction;
}

bool check_d(char d1,char d)
{
    if(d1=='a' || d1=='d')
    {
        if(d=='w'|| d=='s')
            return true;
        else
            return false;
    }
    else if(d1=='w' || d1=='s')
    {
        if(d=='a' || d=='d')
            return true;
        else
            return false;
    }
    else
    {
        return false;
    }
}

void instructions()
{
    cout<<"\n\n";
    cout<<"____________SNAKE GAME______________"<<endl;
    cout<<"\n";
    cout<<"Controls:-"<<endl;
    cout<<"A  -  left\nD  -  right\nW  -  up\nS  -  down\n";
    cout<<"\n\n";
}