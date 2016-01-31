---
layout: post
title: "HackIM writeup (Prog and Crypto)"
description: "Writeup of Nullcon HackIM (programming and crypto sections)"
category: ctf
tags: [writeup, crypto, nullcon, programming, hackim]
imagefeature: cover15.jpg
comments: true
share: true
---

The HackIM 2016 edition has just concluded. Programming questions were pretty straight forward and didn't require much programming skills. Let's have a look at them:

###Programming 1

    So you reached Delhi and now the noise in your head is not allowing you to think rationally. The Nosise in your head has origin its Origin in your Stomach. And this is a big hunger. You can finish one or probably 2 Tandoori Chicken. So where can you get the best Tandoori Chicken in Delhi? This place tweeted last week that the Tandoori Chicken it servers is like never B4. You got its twitter handle?

We have three clues in the question - *Delhi*, *Tandoori Chicken*, and *last week*. Twitter has a fancy [advance search](https://twitter.com/search-advanced) feature in which I entered "Tandoori Chicken" in the section "All of these words", "Delhi" as tweet location and set date between 2016-01-17 and 2016-01-29. The tweet popped up right at the bottom.

<img src="{{ site.url }}/images/hackim-writeup-post/prog1.png" alt="Programming 1" width="550">

**Flag:** @anyahotels

###Programming 2

    Your simple good Deeds can save you but your GREED can kill you. This has happened before. This greedy person lived a miserable life just for the greed of gold and lust. You must know him, once you know him, you must reach his capital and next clues will be given by his famous EX-Body Guard. This file consists of few paragraphs. Each paragraph singles out one Alphabet. Scrambling those Aplphabets will help you to know the country of this Ruler. Who was this Ruler?

File: [TheLastRuler.txt]({{ site.url }}/assets/hackim-writeup-post/TheLastRuler.txt)

Without solving anything it is clearly evident that the name of country will be five letters. I quickly opened the [list of countries](http://bestforpuzzles.com/lists/countries/5.html) with five letters in name. Our problem space has been significantly reduced. But to proceed further we need something more. To determine a pattern, I did a frequency analysis of letters in each paragraph. Here is how it looks:

<img src="{{ site.url }}/images/hackim-writeup-post/prog2.png" alt="Programming 2" width="550"> 

I separated the letter with highest frequency in each paragraph (In first paragraph it is letter 'a') and hence got a word - *aliby*. On re-arranging it slightly we have our country name, *i.e* Libya. Finally I googled "Libya gold ruler" and the first name that popped up in results was the flag.

**Flag:** Muammar Gaddafi

###Programming 3

    Still Hungry and unsutisfied, you are looking for more. Some more, unique un heard dishes. Then you can find one to make it your self. Its his Dish. He has his own website which is he describes as " a social home for each of our passions". The link to his website is on his google+ page. whats the name of his site. By the way he loves and hogs on "Onion Kheer". Have you heard of "Onion Kheer"?

Googling " a social home for each of our passions" reveals the name *Affimity*. On opening their Google+ page, you can see the link to their home page. I don't know why this question is of 300 points.

<img src="{{ site.url }}/images/hackim-writeup-post/prog3.png" alt="Programming 3" width="350">

**Flag:** affimity.com

###Programming 4

    One of the NullCon vidoes talked about a marvalous Russian Gift. The Vidoe was uploaded on [May of 2015] What is the ID of that youtube video.

I don't even know why this question is here. Just a simple google video search reveals the answer.

<img src="{{ site.url }}/images/hackim-writeup-post/prog4.png" alt="Programming 4" width="550">

**Flag:** a4_PvN_A1ts

###Programming 5

    Dont blink your Eyes, you might miss it. But the fatigue and exhaustion rules out any logic, any will to stay awake. What you need now is a slumber. Cat nap will not do. 1 is LIFE and 0 is DEAD. in this GAME OF LIFE sleep is as important food. So... catch some sleep. But Remember...In the world of 10x10 matirx, the Life exists. If you SLOTH, sleep for 7 Ticks, or 7 Generation, In the game of Life can you tell what will be the state of the world? 

    The world- 10x10

    0000000000,0000000000,0001111100,0000000100,0000001000,0000010000,0000100000,0001000000,0000000000,000000000

It is clear that the last row represents a 10x10 matrix. There is a typo in last line, 0 is missing. The initial matrix looks as follows:

    0000000000
    0000000000
    0001111100
    0000000100
    0000001000
    0000010000
    0000100000
    0001000000
    0000000000
    0000000000

A search on wiki for GAME OF LIFE reveals that the next GENERATION or STATE is decided by following rules:

1. Any live cell with fewer than two live neighbours dies, as if caused by under-population.
2. Any live cell with two or three live neighbours lives on to the next generation.
3. Any live cell with more than three live neighbours dies, as if by over-population.
4. Any dead cell with exactly three live neighbours becomes a live cell, as if by reproduction.

We have to apply the above algorithm and find the state after 7 generations. I googled and found the implementation of algo. I made some modifications in it as per the requirements. The final C program looks like:

{% highlight C linenos %}
#include <stdio.h>

/* dimensions of the screen */

#define BOARD_WIDTH 10
#define BOARD_HEIGHT 10

/* set everthing to zero */

void initialize_board (int board[][BOARD_HEIGHT]) {
    int i, j;

    for (i=0; i<BOARD_WIDTH; i++) for (j=0; j<BOARD_HEIGHT; j++)
        board[i][j] = 0;
}

/* add to a width index, wrapping around like a cylinder */

int xadd (int i, int a) {
    i += a;
    while (i < 0) i += BOARD_WIDTH;
    while (i >= BOARD_WIDTH) i -= BOARD_WIDTH;
    return i;
}

/* add to a height index, wrapping around */

int yadd (int i, int a) {
    i += a;
    while (i < 0) i += BOARD_HEIGHT;
    while (i >= BOARD_HEIGHT) i -= BOARD_HEIGHT;
    return i;
}

/* return the number of on cells adjacent to the i,j cell */

int adjacent_to (int board[][BOARD_HEIGHT], int i, int j) {
    int k, l, count;

    count = 0;

    /* go around the cell */

    for (k=-1; k<=1; k++) for (l=-1; l<=1; l++)

        /* only count if at least one of k,l isn't zero */

        if (k || l)
            if (board[xadd(i,k)][yadd(j,l)]) count++;
    return count;
}

void play (int board[][BOARD_HEIGHT]) {
    int i, j, a, newboard[BOARD_WIDTH][BOARD_HEIGHT];

    /* for each cell, apply the rules of Life */

    for (i=0; i<BOARD_WIDTH; i++) for (j=0; j<BOARD_HEIGHT; j++) {
        a = adjacent_to (board, i, j);
        if (a == 2) newboard[i][j] = board[i][j];
        if (a == 3) newboard[i][j] = 1;
        if (a < 2) newboard[i][j] = 0;
        if (a > 3) newboard[i][j] = 0;
    }

    /* copy the new board back into the old board */

    for (i=0; i<BOARD_WIDTH; i++) for (j=0; j<BOARD_HEIGHT; j++) {
        board[i][j] = newboard[i][j];
    }
}

/* print the life board */

void print (int board[][BOARD_HEIGHT]) {
    int i, j;

    for (j=0; j<BOARD_HEIGHT; j++) {

        /* print each column position... */

        for (i=0; i<BOARD_WIDTH; i++) {
            printf ("%c", board[i][j] ? '1' : '0');
        }

        /* followed by a carriage return */

        printf ("\n");
    }

    printf("\n\n");
}

/* read a file into the life board */

void read_file (int board[][BOARD_HEIGHT], char *name) {
    FILE    *f;
    int i, j;
    char    s[100];

    f = fopen (name, "r");
    for (j=0; j<BOARD_HEIGHT; j++) {
        fgets (s, 100, f);

        /* copy the string to the life board */

        for (i=0; i<BOARD_WIDTH; i++) {
            board[i][j] = s[i] == '1';
        }
    }
    fclose (f);
}

/* main program */

int main (int argc, char *argv[]) {
    int board[BOARD_WIDTH][BOARD_HEIGHT], i, j;

    initialize_board (board);
    read_file (board, argv[1]);

    for (i=0; i<7; i++) {
        play (board);
        print (board);
        /* clear the screen using VT100 escape codes */

        // puts ("\033[H\033[J");
    }
}
{% endhighlight %}

On executing the above, we get final state after 7 generations as follows:

    0000000000
    0001100000
    0001111010
    0000001001
    0000001010
    0000000000
    0000000000
    0000000000
    0000000000
    0000000000

This is the final answer.

**Flag:**

    0000000000,0001100000,0001111010,0000001001,0000001010,0000000000,0000000000,0000000000,0000000000,0000000000

* * *

Now let's have a look at the Crypto questions, which I felt were of average difficulty level.

###Crypto 1

    You are in this GAME. A critical mission, and you are surrounded by the beauties, ready to shed their slik gown    s on your beck. On onside your feelings are pulling you apart and another side you are called by the duty. The bigg    iest question is seX OR success? The signals of subconcious mind are not clear, cryptic. You also have the message     of heart which is clear and cryptic. You just need to use three of them and find whats the clear message of your Mi    nd... What you must do?

File: [crypto1.zip](http://ctf.nullcon.net/crypto/crypto1.zip)

We have the following files in the zip:

<img src="{{ site.url }}/images/hackim-writeup-post/crypto1-1.png" alt="Crypto 1-1" width="750">

The cipher used for decoding is the XOR cipher (given in the question) which works as follows:

    plain_text XOR secret = encrypted_text
    encrypted_text XOR secret = plain_text
    encrypted_text XOR plain_text = secret

We have both plain text and cipher text for the heart file, hence we will get the secret by XORing both of them. I used an online [tool](http://xor.pw/) for the same.

First convert the text in both the files to hex. I used a tool for Mac known as Hex Fiend.

<img src="{{ site.url }}/images/hackim-writeup-post/crypto1-2.png" alt="Crypto 1-2" width="750">

Next take the XOR of both the Hex strings

<img src="{{ site.url }}/images/hackim-writeup-post/crypto1-3.png" alt="Crypto 1-3" width="750">

We have our secret key **Its right there what you are looking for. ** which is getting repeated. (Don't forget the blank space after dot). Now use this to decode the mind file. Be careful with the key size, split the encrypted text in chunks of key size. Here is the first 42 bytes of data:

<img src="{{ site.url }}/images/hackim-writeup-post/crypto1-4.png" alt="Crypto 1-4" width="550">

On decoding the complete file you will get a play store [URL](https://play.google.com/store/apps/collection/promotion_3001629_watch_live_games?hl) which says "Never Miss a Game"

**Flag:** Never Miss a Game 

Adding more crypto answers very soon..