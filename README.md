//#include "stdafx.h"
#include "pch.h"
#include <SFML/Graphics.hpp> //using namespace sf
#include <time.h>

/**
* Mod Changes denoted in comments by MOD or MODDED
*/

//globals dimensions for window
int num_vertBox = 30, num_horzBox = 20;
int size = 16; //number of pixels
int w = size * num_horzBox; //background number of pixels in width
int h = size * num_vertBox; //background number of pixels in height

//Create the Snake
int direction, snake_length = 4;

//Create the second Snake for player 2
int direction2, snake2_length = 4;

//Maximum Snake size
struct Snake {
	int x, y;
}s[100];

struct Snake2 {
	int x, y;
}s2[100];

struct Fruit {
	int x, y;
}food;

//MOD added score
struct Score1 {
	int s;
}score1;
struct Score2 {
	int s;
}score2;

//Updating as snake moves
void move() {
	//Head of snake, s[0] depends on direction user inputs
	//if user presses up
	if (direction == 0) {
		s[0].y -= 1;
	}
	//if user presses down
	if (direction == 1) {
		s[0].y += 1;
	}
	//if user presses right
	if (direction == 2) {
		s[0].x += 1;
	}
	//if user presses left
	if (direction == 3) {
		s[0].x -= 1;
	}

	//If Snake eats food it should grow
	if (((s[0].x) == food.x) && ((s[0].y) == food.y))
	{
		//increment snake 
		snake_length++;
		//MOD Score increments
		score1.s++;

		//Randomly place food somewhere else 
		food.x = rand() % num_horzBox;
		food.y = rand() % num_vertBox;
	}

	//loop snake back on other side 
	//LEFT and RIGHT 
	if (s[0].x > num_horzBox)
		s[0].x = 0;
	if (s[0].x < 0)
		s[0].x = num_horzBox;
	//TOP and BOTTOM 
	if (s[0].y > num_vertBox)
		s[0].y = 0;
	if (s[0].y < 0)
		s[0].y = num_vertBox;

	//***NEW*** Check if you eat body of snake 
	for (int i = 1; i < snake_length; i++)
	{
		//Cut Snake body from place eaten 
		if (s[0].x == s[i].x && s[0].y == s[i].y)
			snake_length = i;
	}

	for (int i = snake_length; i > 0; i--) {
		s[i].x = s[i - 1].x;
		s[i].y = s[i - 1].y;
	}
}
//MOD Updating Snake 2
void move2() {
	//Head of snake, s[0] depends on direction user inputs
	//if user presses up
	if (direction2 == 0) {
		s2[0].y -= 1;
	}
	//if user presses down
	if (direction2 == 1) {
		s2[0].y += 1;
	}
	//if user presses right
	if (direction2 == 2) {
		s2[0].x += 1;
	}
	//if user presses left
	if (direction2 == 3) {
		s2[0].x -= 1;
	}

	//If Snake eats food it should grow
	if (((s2[0].x) == food.x) && ((s2[0].y) == food.y))
	{
		//increment snake 
		snake2_length++;
		//MOD Increment Score
		score2.s++;

		//Randomly place food somewhere else 
		food.x = rand() % num_horzBox;
		food.y = rand() % num_vertBox;
	}

	//loop snake back on other side 
	//LEFT and RIGHT 
	if (s2[0].x > num_horzBox)
		s2[0].x = 0;
	if (s2[0].x < 0)
		s2[0].x = num_horzBox;
	//TOP and BOTTOM 
	if (s2[0].y > num_vertBox)
		s2[0].y = 0;
	if (s2[0].y < 0)
		s2[0].y = num_vertBox;

	//***NEW*** Check if you eat body of snake 
	for (int i = 1; i < snake2_length; i++)
	{
		//Cut Snake body from place eaten 
		if (s2[0].x == s2[i].x && s2[0].y == s2[i].y)
			snake2_length = i;
	}

	for (int i = snake2_length; i > 0; i--) {
		s2[i].x = s2[i - 1].x;
		s2[i].y = s2[i - 1].y;
	}
}

int main() {

	srand(time(0));

	//MOD holds current scores
	int currentScore1=score1.s, currentScore2=score2.s;
	//Window that we can play the game in
	sf::RenderWindow window(sf::VideoMode(w, h), "Snake Game");

	//Textures load an image into the GPU MODDED
	sf::Texture t1, t2, t3, t4;
	t1.loadFromFile("images/white.png");
	t2.loadFromFile("images/red.png");
	t3.loadFromFile("images/green.png");
	t4.loadFromFile("images/blue.png");

	//Sprite has physical dimensions MODDED
	sf::Sprite sprite1(t1);
	sf::Sprite sprite2(t2);
	sf::Sprite sprite3(t4);

	//***NEW*** initially place food somewhere on screen 
	food.x = 10;
	food.y = 10;

	sf::Clock clock;
	float timer = 0.0f, delay = 0.1f;

	float time = clock.getElapsedTime().asSeconds();
	clock.restart();
	timer += time;
	float offset = 0;

	while (window.isOpen()) {
		float time = clock.getElapsedTime().asSeconds();
		clock.restart();
		timer += time+offset;

		sf::Event e;

		//Check when the window is closed 
		while (window.pollEvent(e)) {
			if (e.type == sf::Event::Closed) {
				window.close();
			}
		}

		//Control for Snake by user
		if (sf::Keyboard::isKeyPressed(sf::Keyboard::Up)) direction = 0;
		if (sf::Keyboard::isKeyPressed(sf::Keyboard::Down)) direction = 1;
		if (sf::Keyboard::isKeyPressed(sf::Keyboard::Right)) direction = 2;
		if (sf::Keyboard::isKeyPressed(sf::Keyboard::Left)) direction = 3;

		//MOD Control for Snake by user 2
		if (sf::Keyboard::isKeyPressed(sf::Keyboard::W)) direction2 = 0;
		if (sf::Keyboard::isKeyPressed(sf::Keyboard::S)) direction2 = 1;
		if (sf::Keyboard::isKeyPressed(sf::Keyboard::D)) direction2 = 2;
		if (sf::Keyboard::isKeyPressed(sf::Keyboard::A)) direction2 = 3;

		if (timer > delay) {
			timer = 0; //reset timer
			move(); //move Snake one forward
			if (currentScore1 < score1.s) {
				offset += 0.1f;
			}
			move2(); //move Snake two forward
			if (currentScore2 < score2.s) {
				offset += 0.1f;
			}
		}

		//Draw in window
		window.clear(); //get rid of all frames

		//Draw Background
		for (int i = 0; i < num_horzBox; i++) {
			for (int j = 0; j < num_vertBox; j++) {
				sprite1.setPosition(i*size, j*size);
				window.draw(sprite1);
			}
		}

		//Draw Snake after
		for (int i = 0; i < snake_length; i++) {
			sprite2.setPosition(s[i].x*size, s[i].y*size);
			window.draw(sprite2);
		}

		//MOD Draw second Snake
		for (int i = 0; i < snake2_length; i++) {
			sprite2.setPosition(s2[i].x*size+1, s2[i].y*size+1);
			window.draw(sprite2);
		}

		//Draw Fruit MODDED
		sprite3.setPosition(food.x*size, food.y*size);
		window.draw(sprite3);

		window.display();
	}
}
