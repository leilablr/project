# project
#include "enigmeimg.h"

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <time.h>

#include <SDL/SDL.h>
#include <SDL/SDL_ttf.h>
#include <SDL/SDL_mixer.h> 
#include <SDL/SDL_image.h>

int i = 0;
void draw_image(SDL_Surface *img, SDL_Surface *screen, int x, int y) 
{
    if (img == NULL) {
        printf("Error: img is NULL\n");
        return;
    }

	SDL_Rect dsrect;
	dsrect.x = x;
	dsrect.y = y;
	dsrect.w = img->w;
	dsrect.h = img->h;

	SDL_BlitSurface(img, NULL, screen, &dsrect);

}

void init_enigme_img(eng_img *ei)
{

	
	ei->img_back = IMG_Load("back.jpg");
	if (ei->img_back == NULL) 
	{
    	printf("Error: Failed to load bg.jpg: %s\n", IMG_GetError());
    
	}
	ei->rep1=NULL;

	ei->rep2=NULL;

	ei->rep3=NULL;

	ei->rep1_1=NULL;

	ei->rep2_1=NULL;

	ei->rep3_1=NULL;
	ei->img_corr1=IMG_Load("questions/Delusion.png");
	ei->img_corr2=IMG_Load("questions/fear.png");
	ei->img_corr3=IMG_Load("questions/depression.png");
	


	Mix_OpenAudio(44100,MIX_DEFAULT_FORMAT, MIX_DEFAULT_CHANNELS,1024);
	Mix_Music *music;
	music=Mix_LoadMUS("sounds/Daft_Punk.mp3");
	Mix_PlayMusic(music,-1);

/*
	if(!music)
printf("introuvable");
*/

	ei->alea=0;
	ei->res=0;
	ei->choix=0;

}

void animation(SDL_Surface *screen)
{
    int i;
    SDL_Surface *img[73];
    char filename[50];
    for (i = 0; i < 73; i++) 
	{
        sprintf(filename, "correct.png");
        img[i] = IMG_Load(filename);
    }
    for(i = 0; i < 73; i++) 
	{
        draw_image(img[i], screen, 300, 100);
        SDL_Flip(screen);
        SDL_Delay(13); 
    }
    for (i = 0; i < 73; i++) 
	{
        SDL_FreeSurface(img[i]);
    }
}

void animation_sad(SDL_Surface *screen)
{
    int i;
    SDL_Surface *img[44];
    char filename[50];
    for (i = 0; i < 43; i++) 
	{
        sprintf(filename, "incorrect.jpeg");
        img[i] = IMG_Load(filename);
    }
    for(i = 0; i < 43; i++) 
	{
        draw_image(img[i], screen, 290, 300);
        SDL_Flip(screen);
        SDL_Delay(20); 
    }

    for (i = 0; i < 43; i++) 
	{
        SDL_FreeSurface(img[i]);
    }
}

void initText(texte *t)
{
	t->position.x=20;
	t->position.y=140;

	t->ap.x=82;
	t->ap.y=231;
	t->bp.x=275;
	t->bp.y=231;
	t->cp.x=484;
	t->cp.y=231;

	t->textColor.r=148;
	t->textColor.g=146;
	t->textColor.b=105;
	t->font=TTF_OpenFont("texte/Minecraft.ttf",20);
}

void animer(eng_img *ei, SDL_Surface *screen, int x, int y, int *test1, int *test2, int *test3)
{
	if (x > 112 && x < 112+100 && y > 256 && y < 256+100)
	{
		(*test1) = 1;
	}
	else
	{
		(*test1) = 0;
	}

	if (x > 305 && x < 305+100 && y > 256 && y < 256+100)
	{
		(*test2) = 1;
	}
	else
	{
		(*test2) = 0;
	}

	if (x > 514 && x < 514+100 && y > 256 && y < 256+100)
	{		
		(*test3) = 1;
	}
	else
	{
		(*test3) = 0;
	}
}
void process_input(eng_img *ei, SDL_Surface *screen, int *test1, int *test2, int *test3)
{
    SDL_Event event;
    int r = 0;
    int mouse_clicked = 0;
    int x=0,y=0;

    while(SDL_PollEvent(&event))
    {
        switch(event.type)
        {
            case SDL_KEYDOWN:

                if (event.key.keysym.sym == SDLK_a)
		              {
		                  r = 1;
		              }
                else if (event.key.keysym.sym == SDLK_b)
		              {
		                  r = 2;
		              }
                else if (event.key.keysym.sym == SDLK_c)
		              {
		                  r = 3;
		              }
             break;
								
				
			case SDL_MOUSEBUTTONDOWN:
				 
				switch(event.button.button)
				{
                	case SDL_BUTTON_LEFT:
                    x = event.button.x;
                    y = event.button.y;
                    if (x >= 112 && x <= 112 + 100 && y >= 256 && y <= 256 + 119)
		                  {
		                      r= 1;
		                  }
                    if (x >= 305 && x <= 305 + 100 && y >= 239 && y <= 239 + 100)
		                  {
		                      r = 2;
		                  }
                    if (x >= 514 && x <= 514 + 100 && y >= 256 && y <= 256 + 100)
		                  {
		                      r = 3;
		                  }
										break;
                }

				case SDL_MOUSEMOTION:
					SDL_GetMouseState(&x, &y);
					animer(ei, screen, x, y, test1, test2, test3);
         }
	}

    ei->choix = r;
    if (r != 0)
    	{
        ei->choix_prec = ei->choix;
        printf("You have selected option %d\n", r);
    	}
}

void affich_enigme(eng_img *ei,SDL_Surface *screen, int *test1, int *test2, int *test3)
{
    draw_image(ei->img_back, screen,0,0);

    if ((*test1) == 0 )
	{
        draw_image(ei->rep1, screen,112,256);
		if((*test2)==0)
        draw_image(ei->rep2, screen,305,256);
		if((*test3)==0)
        draw_image(ei->rep3, screen,514,256);
 	}

    if ((*test1) == 1)
			draw_image(ei->rep1_1, screen, 100, 244);
	if((*test2)==1)
			draw_image(ei->rep2_1, screen, 293, 244);
	if((*test3)==1)
			draw_image(ei->rep3_1, screen, 502, 244);
}

void generation_alea(eng_img *ei) 
{
    FILE* fichier_rep = NULL;
    FILE* fichier_des_questions = NULL;
    char rep1[100];
    char rep2[100];
    char rep3[100];
    char rep1_1[100];
    char rep2_1[100];
    char rep3_1[100];
    int char_lu_reponses = 0;
    int char_lu_questions = 0;
    int ln_reponse = 1;
    int ln_question = 1;

    srand(time(0));
    ei->alea = rand() % 3 + 1;

    fichier_rep = fopen("fichier_rep.txt", "r");
    fichier_des_questions = fopen("questions.txt", "r");

    if (fichier_rep!= NULL && fichier_des_questions!= NULL) 
	{
        while (char_lu_reponses!= EOF && ln_reponse < ei->alea) 
		{
            char_lu_reponses = fgetc(fichier_rep);
            if (char_lu_reponses == '\n') 
			{
                ln_reponse++;
            }
        }

        fscanf(fichier_rep, "%s %s %s %s %s %s %d", rep1, rep2, rep3,rep1_1,rep2_1,rep3_1, &ei->res);
        fclose(fichier_rep);

        char_lu_questions = 0;
        while (char_lu_questions!= EOF && ln_question < ei->alea) 
		{
            char_lu_questions = fgetc(fichier_des_questions);
            if (char_lu_questions == '\n') 
			{
                ln_question++;
            }
        }

        fgets(ei->ques, 100, fichier_des_questions);
        fclose(fichier_des_questions);

    } 
	else 
	{
        printf("Error: cannot open input files.\n");
    }
}

void resolu_enigme(eng_img *ei, SDL_Surface *screen, int *test1, int *test2, int *test3) 
{
    process_input(ei, screen, test1, test2, test3);
    if (ei->choix != 0) 
	{
        if (ei->choix == ei->res) 
		{
            if (ei->alea == 1) 
			{
                animation(screen);
                draw_image(ei->img_corr1, screen, 45, 29);
            } 
			else if (ei->alea == 2) 
			{
                animation(screen);
                draw_image(ei->img_corr2, screen, 45, 29);
            } 
			else if (ei->alea == 3) 
			{
                animation(screen);
                draw_image(ei->img_corr3, screen, 45, 29);
            }
            SDL_Flip(screen);
            SDL_Event event;
            while (1) 
			{
                SDL_WaitEvent(&event);
                if (event.type == SDL_KEYDOWN) 
				{
                    SDL_Quit();
                    exit(0);
                }
                if (event.type == SDL_QUIT) 
				{
                    SDL_Quit();
                    exit(0);
                }
            }
        } 
		else 
			{
            //draw_image(ei->img_faux, screen, 290, 300);
            animation_sad(screen);
            SDL_Flip(screen);
            SDL_Delay(1000);
        	}
    }
}

void display(texte t, eng_img *ei, SDL_Surface *screen)
{
    t.surfaceTexte = TTF_RenderText_Solid(t.font, ei->ques, t.textColor);
    t.a = TTF_RenderText_Solid(t.font, "A.", t.textColor);
    t.b = TTF_RenderText_Solid(t.font, "B.", t.textColor);
    t.c = TTF_RenderText_Solid(t.font, "C.", t.textColor);
    SDL_BlitSurface(t.surfaceTexte, NULL, screen, &t.position);
    SDL_BlitSurface(t.a, NULL, screen, &t.ap);
    SDL_BlitSurface(t.b, NULL, screen, &t.bp);
    SDL_BlitSurface(t.c, NULL, screen, &t.cp);
}

void liberer(eng_img ei)
{
	SDL_FreeSurface(ei.img_back);
	SDL_FreeSurface(ei.img_corr1);
	SDL_FreeSurface(ei.img_corr2);
	SDL_FreeSurface(ei.img_corr3);
	SDL_FreeSurface(ei.img_faux);
	SDL_FreeSurface(ei.rep1);
	SDL_FreeSurface(ei.rep2);
	SDL_FreeSurface(ei.rep3);
	SDL_FreeSurface(ei.rep1_1);
	SDL_FreeSurface(ei.rep2_1);
	SDL_FreeSurface(ei.rep3_1);

}

void FreeText(texte A)
{
	SDL_FreeSurface (A.surfaceTexte);
	SDL_FreeSurface (A.a);
	SDL_FreeSurface (A.b);
	SDL_FreeSurface (A.c);
}

void sous_menu_sauvegarde(eng_img ei)
{
 FILE *f=NULL;

 f=fopen("sauvegarder.txt","w");
if(f!=NULL)
	{
		fprintf(f,"votre reponse est: %d\n", ei.choix_prec);
		fclose(f);
	}
else
printf("erreur\n");
}



#.h
#ifndef ENIGMEIMG_H_INCLUDED
#define ENIGMEIMG_H_INCLUDED

#include <stdio.h>
#include <stdlib.h>
#include <SDL/SDL.h> 
#include <SDL/SDL_ttf.h> 
#include <SDL/SDL_mixer.h> 
#include <SDL/SDL_image.h> 



typedef struct
{
	SDL_Surface *rep1,*rep2,*rep3,*rep1_1,*rep2_1,*rep3_1, *rep1_1_def,*rep2_1_def,*rep3_1_def;
	SDL_Rect img_p, rep1_pos,rep2_pos,rep3_pos,rep1_pos_1,rep2_pos_1,rep3_pos_1; 
	SDL_Surface *img_corr1,*img_corr2,*img_corr3, *img_faux;
	SDL_Rect img_reponse_pos;
	SDL_Surface *img_back;
	SDL_Rect img_back_pos;

	int alea,res,choix,choix_prec,mouse;
	char ques[100];

}eng_img;



typedef struct
{
	TTF_Font *font;
	SDL_Color textColor;
	SDL_Surface *surfaceTexte,*a,*b,*c;
	SDL_Rect position,ap,bp,cp; 
	char texte[50];
}texte;

void draw_image(SDL_Surface *img, SDL_Surface *screen, int x, int y);
void init_enigme_img(eng_img *ei);
void animation(SDL_Surface *screen);
void animation_sad(SDL_Surface *screen);
void affich_enigme(eng_img *ei,SDL_Surface *screen, int *test1, int *test2, int *test3);
void process_input(eng_img *ei, SDL_Surface *screen, int *test1, int *test2, int *test3);
void resolu_enigme(eng_img *ei,SDL_Surface *screen, int *test1, int *test2, int *test3);
void generation_alea(eng_img *ei);
void sous_menu_sauvegarde(eng_img ei); 
void animer(eng_img *ei, SDL_Surface *screen, int x, int y, int *test1, int *test2, int *test3);
void animation_story(SDL_Surface *screen);
void cineee(SDL_Surface *screen);
void display(texte t,eng_img *ei,SDL_Surface *screen);
void initText(texte *t);
void FreeText(texte A);
void liberer(eng_img ei);

#endif





#main.c
#include "enigmeimg.h"
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <time.h>
#include <SDL/SDL.h>
#include <SDL/SDL_image.h>
#include <SDL/SDL_ttf.h>
#include <SDL/SDL_mixer.h> 

#define SCREEN_W 720
#define SCREEN_H 808

int main(int argc, char **argv)
{
    
    SDL_Surface *screen;
    SDL_Event event;
    int continuer = 1;
    eng_img ei;
    texte t;
    int test1 = 0, test2 = 0, test3 = 0;
    int i = 0;

    if(SDL_Init(SDL_INIT_VIDEO) < 0)
    {
        printf("SDL initialization failed. SDL_Error: %s\n", SDL_GetError());
        return -1;
    }
    
    if(TTF_Init() < 0)
    {
        printf("TTF initialization failed. TTF_Error: %s\n", TTF_GetError());
        return -1;
    }

   

    initText(&t);

    screen = SDL_SetVideoMode(SCREEN_W, SCREEN_H, 32, SDL_HWSURFACE | SDL_DOUBLEBUF);
    if(!screen)
    {
        printf("Erreur : %s\n", SDL_GetError());
        return 1;
    }

    Mix_Music *music = Mix_LoadMUS("sounds/Daft_Punk.mp3");
if (music == NULL) {
    printf("Error loading music: %s\n", Mix_GetError());
    return -1;
}


    init_enigme_img(&ei);
    generation_alea(&ei);
    printf("%d %d %d \n", ei.alea, ei.res, ei.choix);
    while(continuer == 1)
    {

        affich_enigme(&ei,&screen,&test1,&test2,&test3);
        display(t, &ei, screen);
        resolu_enigme(&ei, screen, &test1, &test2, &test3);
        //animation(screen);
        
				

        while(SDL_PollEvent(&event))
        {
            switch(event.type)
            {
                case SDL_QUIT:
                    continuer = 0;
                    break;
                case SDL_KEYDOWN:
                    if(event.key.keysym.sym == SDLK_q || event.key.keysym.sym == SDLK_ESCAPE)
                    {
                        continuer = 0;
                    }
                    break;
            }
        }	
			SDL_Flip(screen);
    }

    sous_menu_sauvegarde(ei);
    liberer(ei);
    FreeText(t);
    TTF_CloseFont(t.font);
    TTF_Quit();
    SDL_Quit();
    
    return 0;
}
