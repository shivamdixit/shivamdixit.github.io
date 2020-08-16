---
title: "HackIM 2016 write-up (Prog and Crypto)"
date: 2016-01-31
description: "Writeup of Nullcon HackIM 2016 (programming and crypto questions)"
categories: ["CTF"]
tags: ["writeup", "crypto", "nullcon", "programming", "hackim"]
draft: false
---

The HackIM 2016 edition has just concluded. Programming questions were pretty straight forward and didn't require much programming skills. Let's have a look at them:

### Programming 1

    So you reached Delhi and now the noise in your head is not allowing you to think rationally. The Nosise in your head has origin its Origin in your Stomach. And this is a big hunger. You can finish one or probably 2 Tandoori Chicken. So where can you get the best Tandoori Chicken in Delhi? This place tweeted last week that the Tandoori Chicken it servers is like never B4. You got its twitter handle?

We have three clues in the question - *Delhi*, *Tandoori Chicken*, and *last week*. Twitter has a fancy [advance search](https://twitter.com/search-advanced) feature in which I entered "Tandoori Chicken" in the section "All of these words", "Delhi" as tweet location and set date between 2016-01-17 and 2016-01-29. The tweet popped up right at the bottom.

{{< figure src="/images/hackim-writeup-post/prog1.png" alt="Programming 1" >}}

**Flag:** @anyahotels

### Programming 2

    Your simple good Deeds can save you but your GREED can kill you. This has happened before. This greedy person lived a miserable life just for the greed of gold and lust. You must know him, once you know him, you must reach his capital and next clues will be given by his famous EX-Body Guard. This file consists of few paragraphs. Each paragraph singles out one Alphabet. Scrambling those Aplphabets will help you to know the country of this Ruler. Who was this Ruler?

File: [TheLastRuler.txt](/assets/hackim-writeup-post/TheLastRuler.txt)

Without solving anything it is clearly evident that the name of country will be five letters. I quickly opened the [list of countries](http://bestforpuzzles.com/lists/countries/5.html) with five letters in name. Our problem space has been significantly reduced. But to proceed further we need something more. To determine a pattern, I did a frequency analysis of letters in each paragraph. Here is how it looks:

{{< figure src="/images/hackim-writeup-post/prog2.png" alt="Programming 2" >}}

I separated the letter with highest frequency in each paragraph (In first paragraph it is letter 'a') and hence got a word - *aliby*. On re-arranging it slightly we have our country name, *i.e* Libya. Finally I googled "Libya gold ruler" and the first name that popped up in results was the flag.

**Flag:** Muammar Gaddafi

### Programming 3

    Still Hungry and unsutisfied, you are looking for more. Some more, unique un heard dishes. Then you can find one to make it your self. Its his Dish. He has his own website which is he describes as " a social home for each of our passions". The link to his website is on his google+ page. whats the name of his site. By the way he loves and hogs on "Onion Kheer". Have you heard of "Onion Kheer"?

Googling " a social home for each of our passions" reveals the name *Affimity*. On opening their Google+ page, you can see the link to their home page. I don't know why this question is of 300 points.

{{< figure src="/images/hackim-writeup-post/prog3.png" alt="Programming 3" >}}

**Flag:** affimity.com

### Programming 4

    One of the NullCon vidoes talked about a marvalous Russian Gift. The Vidoe was uploaded on [May of 2015] What is the ID of that youtube video.

I don't even know why this question is here. Just a simple google video search reveals the answer.

{{< figure src="/images/hackim-writeup-post/prog4.png" alt="Programming 4" >}}

**Flag:** a4_PvN_A1ts

### Programming 5

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

```C {linenos=true}
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
```

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

### Crypto 1

    You are in this GAME. A critical mission, and you are surrounded by the beauties, ready to shed their slik gown    s on your beck. On onside your feelings are pulling you apart and another side you are called by the duty. The bigg    iest question is seX OR success? The signals of subconcious mind are not clear, cryptic. You also have the message     of heart which is clear and cryptic. You just need to use three of them and find whats the clear message of your Mi    nd... What you must do?

File: [crypto1.zip](http://ctf.nullcon.net/crypto/crypto1.zip)

We have the following files in the zip:

{{< figure src="/images/hackim-writeup-post/crypto1-1.png" alt="Crypto 1-1" >}}

The cipher used for decoding is the XOR cipher (given in the question) which works as follows:

    plain_text XOR secret = encrypted_text
    encrypted_text XOR secret = plain_text
    encrypted_text XOR plain_text = secret

We have both plain text and cipher text for the heart file, hence we will get the secret by XORing both of them. I used an online [tool](http://xor.pw/) for the same.

First convert the text in both the files to hex. I used a tool for Mac known as Hex Fiend.

{{< figure src="/images/hackim-writeup-post/crypto1-2.png" alt="Crypto 1-2" >}}

Next take the XOR of both the Hex strings

{{< figure src="/images/hackim-writeup-post/crypto1-3.png" alt="Crypto 1-3" >}}

Now we have the secret key -> *Its right there what you are looking for. * (which is ofcourse being repeated). Don't forget the blank space after dot, it is also part of the key. Now use this to decode the cipher text in mind_crypt.txt. Be careful with the key size, split the encrypted text in chunks of key size. Here is the first 42 bytes of data:

{{< figure src="/images/hackim-writeup-post/crypto1-4.png" alt="Crypto 1-4" >}}

On decoding the complete file you will get a play store [URL](https://play.google.com/store/apps/collection/promotion_3001629_watch_live_games?hl) which says "Never Miss a Game"

**Flag:** Never Miss a Game

### Crypto 2
    Some one was here, some one had breached the security and had infiltrated here. All the evidences are touched, Logs are altered, records are modified with key as a text from book.The Operation was as smooth as CAESAR had Conquested Gaul. After analysing the evidence we have some extracts of texts in a file. We need the title of the book back, but unfortunately we only have a portion of it...

File: [The_extract.txt](http://ctf.nullcon.net/crypto/The_extract.txt)

It is a Caesar cipher (given) with unknown key. Hence I used a [tool](http://www.xarg.org/tools/caesar-cipher/) which can "guess" the key. The key turned out to be **16**, and the deciphered text was as follows:

{{< figure src="/images/hackim-writeup-post/crypto2.png" alt="Crypto 2" >}}

On googling the extract you will find the exact name of the book, which is our flag.

**Flag:** In the Shadow of Greed

### Crypto 3

    After entring the luxurious condomium,you get the feel that you are in home of a yester Star. the extravagant flooring and furnishings shows the richness of this star. But where is she? There she is, lying peacefuly on her couch. See what Envy has done to her...with a perfectly well maintained attractive body she still looks sex diva, except for her face beyond recogniton. Her identity is crucial to know who killed her and why? In absence of any personal data around there is only a file. with a cryptic text in it. Preity sure she has used her own name to XOR encrypt the file. And challenge is to know her name.

File: [AncientSecretsOfTheKamaSutra.txt](http://ctf.nullcon.net/crypto/AncientSecretsOfTheKamaSutra.txt)

This question is same as Crypto 1 except that we don't have the plain text of the given cipher text. Therefore it becomes bit trickier to find the key. There are many algorithms and theories around breaking the repeated key XOR, such as on [matasano](http://cryptopals.com/sets/1/challenges/6/), [web](https://wiremask.eu/tools/xor-cracker/), etc. They are time consuming and I was already running short of time. Therefore I decided to follow a different approach, *i.e* try finding the plain text directly. Although this might sound bizarre at first and might not work everytime, but I was lucky this time. I googled the name of file *Ancient Secrets Of The KamaSutra*, it turned out to be a movie. I copied the first few letters (*Julie and Steve's*) from the imdb story line (17 bytes) and converted them to hex, and XORed it with the first 17 bytes of the cipher text.

{{< figure src="/images/hackim-writeup-post/crypto3-1.png" alt="Crypto 3-1" >}}

{{< figure src="/images/hackim-writeup-post/crypto3-2.png" alt="Crypto 3-2" >}}

Bingo! We have the name that we were looking for. Just a sidenote, this name is also present in the cast on imdb.

**Flag:** Jeanna Fine

### Crypto 4

    He is influential, he is powerful. He is your next contact you can get you out of this situation. You must reach him soon. Who is he? The few pointers intrecpted by KGB are in the file. Once we know him, we can find his most valuable possession, his PRIDE.

File: [whatsHisPride.md5s](http://ctf.nullcon.net/crypto/whatsHisPride.md5s)

This was more of a hit-and-try problem. MD5 hashes were given which can be decrypted using an online tool. The output of MD5 decryption was - *Carrie Grease Perfect Shout Basic Actor Aircraft name*. On googling, you will get the name **John Travolta**. The first part of question is solved where we had to find the name. Now the next part is to find his pride.

I followed the wiki article and searched for PRIDE and it's synonyms. On searching *honor* I found the key which got accepted at last. (I tried ~30 keys before my solution was accepted)

**Flag:** Jett Clipper Ella

### Crypto 5

    Now you are one step away from knowing who is that WARRIOR. The Fighter who will decide the fate of war between the 2 countries. The Pride of One and Envey of the Other... You have got the secrete file which has the crucial information to identify the fighter. But the file is encrypted with a RSA-Private key. Good news you have its corresponding public key in a file. Bad news there are 49 other keys. Whos is the Fighter.

File: [crypto5.zip](http://ctf.nullcon.net/crypto/crypto5.zip)

It was purely a Brute Force question. Firstly I separated out the keys using AWK:

```bash
awk '/BEGIN PUBLIC KEY/{n++}{print >"out" n ".pub" }' all_keys.txt
```

At this point, I had 50 different public keys in different files, now I just had to find the correct key file. I wrote a small shell script to know it:

```bash {linenos=true}
#!/bin/bash

for i in `seq 1 50`
do
    openssl rsautl -inkey "out$i.pub" -pubin -in warrior.txt > /dev/null 2>&1

    if [ $? -eq 0 ]
    then
    echo "Value of i is $i"
    openssl rsautl -inkey "out$i.pub" -pubin -in warrior.txt
    exit 0
    fi
done
```

The output of the script was:

    Value of i is 26
    This fighter is a designation for two separate, heavily upgraded derivatives of the Su-35 'Flanker' jet plane. They are single-seaters designed by Sukhoi(KnAAPO).

On googling the paragraph I found the name of fighter on wikipedia.

{{< figure src="/images/hackim-writeup-post/crypto5.png" alt="Crypto 5" >}}

**Flag:** Sukhoi Su-35

This covers both Programming and Crypto questions. Write-up for other questions coming soon...
