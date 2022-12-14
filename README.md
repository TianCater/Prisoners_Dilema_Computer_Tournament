README
================
2022-09-16

## Purpose

This paper conducts a computer tournament to study effective choice in
the iterated prisoner’s dilemma game. Drawing from Axelrod (1980)’s
computer tournament, we will distinguish between two types of games, one
in which the number of rounds played is known by each player (or
strategy) beforehand, (Round 1) and the other where it is determined
probabilistically, effectively purging some minor end- game effects
(Round 2). Both games allow for mutual gains from cooperation and
possible exploitation of a strategy by another strategy. A preliminary
observation is that there is no one best strategy. Table 3.1 provides a
summary of some of the most competitive strategies that have been
played.

The code below programs a Repeated Prisoner’s Dilemma Computer Game for
multiple strategies to compete against one another.

NB — Dont run the last code chunk before you have investigated the
“demo” code in more detail.

NB — To get the PDF of the final project, Knit the Rmd file (not this
README). ALSO, dont commit to github the this PDF, first delete it
before committing to this repository.

``` r
text_tbl <- data.frame(
Strategy = c("Tit-for-Tat (TFT)", "Generous TFT", "Tit-for-Two-Tat","DOWNING","JOSS","AllC","AllD","Alternate","Grudger","Random","Detective","Win-Stay-Lose-Shift"),
Description = c(
"Begins by cooperating and then simply repeats the last moves made by the opponent.",
"Same as TFT but 'forgives' defections in 1/3 of cases.",
"Like TFT but only retaliates after two defections rather than one.",
"Based on outcome maximization principle, if other player is responsive to DOWNING, it will cooperate, if not, will defect.",
"This strategy plays Tit For Tat, always defecting if the opponent defects but cooperating when the opponent cooperates with probability .9.",
"Always cooperates.",
"Always defects.",
"Randomly cooperate or defect (prob = 1/2) on the 1st round then alternates regardless of what the opponent does.",
"Cooperates until the opponent defects and then defects forever.",
"This strategy plays randomly (prob = 1/2) disregarding the history of play.",
"Cooperates, defects, cooperates and cooperates again. If the opponent doesn't relatilates in the 3rd round, defects all the time;otherwise plays Tit-for-Tat.",
" Cooperates first then, if the opponent cooperated on the last round, repeat last move; otherwise, switch.")
)

kbl(text_tbl, booktabs = T) %>%
kable_styling(full_width = F) %>%
column_spec(1, bold = T)
```

<table class="table" style="width: auto !important; margin-left: auto; margin-right: auto;">
<thead>
<tr>
<th style="text-align:left;">
Strategy
</th>
<th style="text-align:left;">
Description
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:left;font-weight: bold;">
Tit-for-Tat (TFT)
</td>
<td style="text-align:left;">
Begins by cooperating and then simply repeats the last moves made by the
opponent.
</td>
</tr>
<tr>
<td style="text-align:left;font-weight: bold;">
Generous TFT
</td>
<td style="text-align:left;">
Same as TFT but ‘forgives’ defections in 1/3 of cases.
</td>
</tr>
<tr>
<td style="text-align:left;font-weight: bold;">
Tit-for-Two-Tat
</td>
<td style="text-align:left;">
Like TFT but only retaliates after two defections rather than one.
</td>
</tr>
<tr>
<td style="text-align:left;font-weight: bold;">
DOWNING
</td>
<td style="text-align:left;">
Based on outcome maximization principle, if other player is responsive
to DOWNING, it will cooperate, if not, will defect.
</td>
</tr>
<tr>
<td style="text-align:left;font-weight: bold;">
JOSS
</td>
<td style="text-align:left;">
This strategy plays Tit For Tat, always defecting if the opponent
defects but cooperating when the opponent cooperates with probability
.9.
</td>
</tr>
<tr>
<td style="text-align:left;font-weight: bold;">
AllC
</td>
<td style="text-align:left;">
Always cooperates.
</td>
</tr>
<tr>
<td style="text-align:left;font-weight: bold;">
AllD
</td>
<td style="text-align:left;">
Always defects.
</td>
</tr>
<tr>
<td style="text-align:left;font-weight: bold;">
Alternate
</td>
<td style="text-align:left;">
Randomly cooperate or defect (prob = 1/2) on the 1st round then
alternates regardless of what the opponent does.
</td>
</tr>
<tr>
<td style="text-align:left;font-weight: bold;">
Grudger
</td>
<td style="text-align:left;">
Cooperates until the opponent defects and then defects forever.
</td>
</tr>
<tr>
<td style="text-align:left;font-weight: bold;">
Random
</td>
<td style="text-align:left;">
This strategy plays randomly (prob = 1/2) disregarding the history of
play.
</td>
</tr>
<tr>
<td style="text-align:left;font-weight: bold;">
Detective
</td>
<td style="text-align:left;">
Cooperates, defects, cooperates and cooperates again. If the opponent
doesn’t relatilates in the 3rd round, defects all the time;otherwise
plays Tit-for-Tat.
</td>
</tr>
<tr>
<td style="text-align:left;font-weight: bold;">
Win-Stay-Lose-Shift
</td>
<td style="text-align:left;">
Cooperates first then, if the opponent cooperated on the last round,
repeat last move; otherwise, switch.
</td>
</tr>
</tbody>
</table>

``` r
#column_spec(2, width = "30em")
```

``` r
# ========================================================================== #
# Basic algos
# ========================================================================== #

# These are the algos included into the tournament anyway.

# -------------------------------------------------------------------------- #
# AllC
# -------------------------------------------------------------------------- #

# Always cooperates.

allc = function(p, o, n = 2000) TRUE


# -------------------------------------------------------------------------- #
# AllD
# -------------------------------------------------------------------------- #

# Always defects.

alld = function(p, o, n = 2000) FALSE


# -------------------------------------------------------------------------- #
# Alternate
# -------------------------------------------------------------------------- #

# Randomly cooperate or defect (prob = 1/2) on the 1st round then alternates
# regardless of what the opponent does.

alt = function(p, o, n = 2000) {
    ifelse(length(p) == 0, sample(c(T, F), 1), !tail(p, 1))
}


# -------------------------------------------------------------------------- #
# Grudger
# -------------------------------------------------------------------------- #

# Cooperates until the opponent defects and then defects forever.

grudger = function(p, o, n = 2000) {
    all(o)
}


# -------------------------------------------------------------------------- #
# Random
# -------------------------------------------------------------------------- #

# This strategy plays randomly (prob = 1/2) disregarding the history of play.

rand = function(p, o, n = 2000) {
    sample(c(TRUE, FALSE), 1)
}


# -------------------------------------------------------------------------- #
# Detective
# -------------------------------------------------------------------------- #

# Cooperates, defects, cooperates and cooperates again. If the opponent
# doesn't relatilates in the 3rd round, defects all the time; otherwise plays
# Tit-for-Tat.

detect = function(p, o, n = 2000) {
    r <- length(p)+1
    if(r < 5) {
        res <- as.logical(r != 2)
    } else {
        res <- ifelse(o[3], FALSE, o[r-1])
    }
    res
}


# -------------------------------------------------------------------------- #
# Tit-For-Tat
# -------------------------------------------------------------------------- #

# Begins by cooperating and then simply repeats the last moves made by the
# opponent.

tft = function(p, o, n = 2000) {
    ifelse(length(p) == 0, TRUE, tail(o, 1))
}


# -------------------------------------------------------------------------- #
# Generous Tit-For-Tat
# -------------------------------------------------------------------------- #

# Same as TFT but 'forgives' defections in 1/3 of cases.

gtft = function(p, o, n = 2000) {
    res <- TRUE
    if(length(p) > 0 && !tail(o, 1)) res <- sample(c(F, T), 1,, 2:1) 
    res
}


# -------------------------------------------------------------------------- #
# Tit-For-Two-Tats
# -------------------------------------------------------------------------- #

# Like TFT but only retaliates after two defections rather than one.

tf2t = function(p, o, n = 2000) {
    res <- TRUE
    if(length(p) > 1) res <- ! all(! tail(o, 2))
    res
}


# -------------------------------------------------------------------------- #
# Win-Stay-Lose-Shift
# -------------------------------------------------------------------------- #

# Cooperates first then, if the opponent cooperated on the last round, repeat
# last move; otherwise, switch.

wsls = function(p, o, n = 2000) {
    r <- length(p)+1
    if(r == 1) {
        res <- TRUE
    } else {
        res <- ifelse(tail(o, 1), tail(p, 1), !tail(p, 1))
    }
    res
}


# -------------------------------------------------------------------------- #
# Zero-Determinant GTFT 2
# -------------------------------------------------------------------------- #

# Always collaborates if the opponent did. When the opponent defects, it
# collaborates with a probability of 1/8 if it has collaborated on the last
# round or with a probability of 1/4 otherwise.

zdgtft2 = function(p, o, n = 2000) {
    if(length(p) == 0) {
        res <- TRUE
    } else {
        if(tail(o, 1)) {
            res <- TRUE
        } else {
            z <- ifelse(tail(p, 1), 1/8, 1/4)
            res <- sample(c(T, F), 1,, c(z, 1-z))
        }
    }
    res
}


# -------------------------------------------------------------------------- #
# Extort 2
# -------------------------------------------------------------------------- #

# If the opponent collarorated on the last round, it will collaborate with a
# probability of 8/9 if it has also collaborated and 1/3 otherwise. If the
# opponent defected, it will collaborate with a probability of 1/2 if it
# collaborated and 0 otherwise.

extort2 = function(p, o, n = 2000) {
    if(length(p) == 0) {
        res <- TRUE
    } else {
        if(tail(o, 1)) {
            z <- ifelse(tail(p, 1), 8/9, 1/3)
        } else {
            z <- ifelse(tail(p, 1), 1/2, 0)
        }
        res <- sample(c(TRUE, FALSE), 1, prob = c(z, 1-z))
    }
    res
}


# -------------------------------------------------------------------------- #
# Nydegger
# -------------------------------------------------------------------------- #

# This strategy begins by playing Tit For Tat for the first 3 rounds with the
# following modifications: if it is the only strategy to cooperate in the
# first round and the only strategy to defect on the second round then it
# defects on the 3 round (despite the fact that TFT would now cooperate).
# After these first 3 rounds the next move is made depending on the previous 3
# rounds. A score is given to these rounds according to the following
# calculation:
# A = 16a_1 + 4a_2 + a_3
# Where a_i is dependent on the outcome of the previous i^th round. If both
# strategies defect, a_i = 3, if the opponent only defects: a_i = 2 and 
# finally if it is only this strategy that defects then a_i = 1 (*).
# Finally this strategy defects if and only if:
# A \in {1,6,7,17,22,23,26,29,30,31,33,38,39,45,49,54,55,58,61}
# This strategy came 3rd in Axelrod’s original tournament.

# (*) I've assumed that if none defect then a_i = 0.

nydegger = function(p, o, n = 2000) {
    r <- length(p)+1
    V <- c(1, 6, 7, 17, 22, 23, 26, 29, 30, 31, 33, 38, 39, 45, 49, 54, 55,
         58, 61)
    if(r %in% 1:3) {
        if(r == 1) {
            res <- TRUE
        }
        if(r == 2) {
            res <- tail(o, 1)
        }
        if(r == 3) {
            res <- tail(o, 1)
            if((p[1] & !o[1]) & (!p[2] & o[2])) res <- FALSE
        }
    } else {
        a1 <- a2 <- a3 <- 0
        if(!p[r-1] & !o[r-1]) {
            a1 <- 3
        } else {
            a1 <- ifelse(!o[r-1], 2, 1)
        }
        if(!p[r-2] & !o[r-2]) {
            a2 <- 3
        } else {
            a2 <- ifelse(!o[r-2], 2, 1)
        }
        if(!p[r-3] & !o[r-3]) {
            a3 <- 3
        } else {
            a3 <- ifelse(!o[r-3], 2, 1)
        }
        A <- 16*a1+4*a2+a3
        res <- ! A %in% V
    }
    res
}


# -------------------------------------------------------------------------- #
# Grofman
# -------------------------------------------------------------------------- #

# This is a pretty simple strategy: it cooperates on the first two rounds and
# returns the opponent’s last action for the next 5. For the rest of the game
# Grofman cooperates if both players selected the same action in the previous
# round, and otherwise cooperates randomly with probability 2/7

grofman = function(p, o, n = 2000) {
    r <- length(p)+1
    if(r %in% 1:2) {
        res <- TRUE
    }
    if(r %in% 3:7) {
        res <- tail(o, 1)
    }
    if(r > 7) {
        if(tail(p, 1) == tail(o, 1)) {
            res <- TRUE
        } else {
            res <- sample(c(T, F), 1, prob = c(2, 5)/7)
        }
    }
    res
}


# -------------------------------------------------------------------------- #
# Shubik
# -------------------------------------------------------------------------- #

# This strategy plays a modification of Tit For Tat. It starts by retaliating
# with a single defection but the number of defections increases by 1 each
# time the opponent defects when this strategy cooperates.

shubik = function(p, o, n = 2000) {
    r <- length(p)+1
    if(r == 1) {
        res <- TRUE
    } else {
        n <- max(1, sum(p & !o))
        d <- tail(which(!o), 1)
        punish <- ifelse(length(d) == 0, FALSE, r <= d+n)
        res <- tail(o, 1) & !punish
    }
    res
}


# -------------------------------------------------------------------------- #
# Davis
# -------------------------------------------------------------------------- #

# This strategy is a modification of Grudger. It starts by cooperating for the
# first 10 moves and then plays Grudger (*).
# (*) I've assumed it only plays Grudger using data from round 10 onward...

davis = function(p, o, n = 2000) {
    r <- length(p)+1
    if(r < 11) {
        res <- TRUE
    } else {
        res <- all(o[10:length(o)])
    }
    res
}


# -------------------------------------------------------------------------- #
# Joss
# -------------------------------------------------------------------------- #

# This strategy plays Tit For Tat, always defecting if the opponent defects
# but cooperating when the opponent cooperates with probability .9.

joss = function(p, o, n = 2000) {
    if(length(p) == 0) {
        res <- TRUE
    } else {
        if(tail(o, 1)) {
            res <- sample(c(T, F), 1, prob = c(.9, .1))
        } else {
            res <- FALSE
        }
    }
    res
}


# -------------------------------------------------------------------------- #
# Tullock
# -------------------------------------------------------------------------- #

# This strategy cooperates for the first 11 rounds and then (randomly)
# cooperates 10% less often than the opponent has in the previous 10 rounds.

tullock = function(p, o, n = 2000) {
    r <- length(p)+1
    if(r < 12) {
        res <- TRUE
    } else {
        z <- .9 * sum(tail(o, 10))/10
        res <- sample(c(T, F), 1, prob = c(z, 1-z))
    }
    res
}


# -------------------------------------------------------------------------- #
# Eatherley
# -------------------------------------------------------------------------- #

# Generally cooperates unless the opponent defects, in which case Eatherley
# defects with a probability equal to the proportion of rounds that the
# opponent has defected.

eatherley = function(p, o, n = 2000) {
    if(all(o)) {
        res <- TRUE
    } else {
        z <- sum(!o)/length(o)
        res <- sample(c(F, T), 1, prob = c(z, 1-z))
    }
    res
}

# -------------------------------------------------------------------------- #
# Champion
# -------------------------------------------------------------------------- #

# Operates in three phases: The first phase lasts for the first 1/20-th of the
# rounds and Champion always cooperates. In the second phase, lasting until
# 4/50-th of the rounds have passed, Champion mirrors its opponent’s last
# last move. In the last phase, Champion cooperates unless
# - the opponent defected on the last round, and
# - the opponent has cooperated less than 60% of the rounds, and
# - a random number is greater than the proportion of rounds defected (*).

# I assume it means defected *by the opponent*.

champion = function(p, o, n = 2000) {
    r <- length(p)+1
    v <- round(c(n/20, n/20+n*4/50))
    if(r %in% 1:v[1]) {
        res <- TRUE
    }
    if(r %in% (v[1]+1):(v[2])) {
        res <- tail(o, 1)
    }
    if(r > v[2]) {
        z <- sum(o)/length(o)
        c1 <- !tail(o, 1)
        c2 <- z < .6
        c3 <- runif(1) > (1-z)
        res <- ifelse(c1 & c2 & c3, FALSE, TRUE)
    }
    res
}


# -------------------------------------------------------------------------- #
# Tester
# -------------------------------------------------------------------------- #

# This strategy is a TFT variant that attempts to exploit certain strategies.
# It defects on the first move. If the opponent ever defects, Tester
# 'apologies' by cooperating and then plays TFT for the rest of the game.
# Otherwise Tester alternates cooperation and defection.

# Not sure I properly interpreded it. I assumed it defects first to see how
# the opponent reacts to its defection. So it must do something at round 2
# given it doesn't know yet what the reaction is. I assumed it collaborates.
# Also, that version plays TFT if the opponent reacted negatively on the round
# and alternates otherwhise.

tester = function(p, o, n = 2000) {
    r <- length(p)+1
    if(r %in% 1:2) {
        res <- c(FALSE, TRUE)[r]
    } else {
        if(! o[2]) {
            res <- ifelse(r == 3, TRUE, tail(o, 1))
        } else {
            res <- !tail(p, 1)
        }
    }
    res
}


# ========================================================================== #
# Not implemented (yet)
# ========================================================================== #

# -------------------------------------------------------------------------- #
# Stein and Rapoport
# -------------------------------------------------------------------------- #

# This strategy plays a modification of Tit For Tat.
# 1. It cooperates for the first 4 moves.
# 2. It defects on the last 2 moves.
# 3. Every 15 moves it makes use of a chi-squared test to check if the
#    opponent is playing randomly.


# -------------------------------------------------------------------------- #
# Tideman and Chieruzzi
# -------------------------------------------------------------------------- #

# This strategy begins by playing Tit For Tat and then things get slightly
# complicated:
# 1. Every run of defections played by the opponent increases the number of
#    defections that this strategy retaliates with by 1.
# 2. The opponent is given a ‘fresh start’ if:
#    - it is 10 points behind this strategy
#    - and it has not just started a run of defections
#    - and it has been at least 20 rounds since the last ‘fresh start’
#    - and there are more than 10 rounds remaining in the tournament
#    - and the total number of defections differs from a 50-50 random sample
#      by at least 3.0 standard deviations.
# A ‘fresh start’ is a sequence of two cooperations followed by an
# assumption that the game has just started (everything is forgotten).


# -------------------------------------------------------------------------- #
# Graaskamp
# -------------------------------------------------------------------------- #

# This strategy follows the following rules:
# 1. Play Tit For Tat for the first 50 rounds;
# 2. Defects on round 51;
# 3. Plays 5 further rounds of Tit For Tat;
# 4. A check is then made to see if the opponent is playing randomly in which
#    case it defects for the rest of the game;
# 5. The strategy also checks to see if the opponent is playing Tit For Tat or
#    another strategy from a preliminary tournament called 'Analogy'. If so it
#    plays Tit For Tat. If not it cooperates and randomly defects every 5 to
#    15 moves.


# -------------------------------------------------------------------------- #
# Downing
# -------------------------------------------------------------------------- #

# This strategy attempts to estimate the next move of the opponent by
# estimating the probability of cooperating given that they defected (P(C|D))
# or cooperated on the previous round (P(C|C)). These probabilities are
# continuously updated during play and the strategy attempts to maximise the
# long term play. Note that the initial values are:
# P(C|C) = P(C|D) = 1/2


# -------------------------------------------------------------------------- #
# Feld
# -------------------------------------------------------------------------- #

# This strategy plays Tit For Tat, always defecting if the opponent defects
# but cooperating when the opponent cooperates with a gradually decreasing
# probability until it is only 1/2.


# -------------------------------------------------------------------------- #
# Unnamed Strategy
# -------------------------------------------------------------------------- #

# This strategy cooperates with a given probability P. This probability (which
# has initial value .3) is updated every 10 rounds based on whether the
# opponent seems to be random, very cooperative or very uncooperative.
# Furthermore, if after round 130 the strategy is losing then P is also
# adjusted.
# Original code not available...
```

``` r
# ========================================================================== #
# Utilities
# ========================================================================== #

# -------------------------------------------------------------------------- #
# .match
# -------------------------------------------------------------------------- #

# Runs a n-round match between 'f1' and 'f2' and returns a list with S, a
# matrix of each algo's mooves and P, a matrix containing their respective
# gains.
# You may also modify the payment matrix with :
# * t: the points earned by defectors when their opponents cooperate,
# * r: the points earned by both algos cooperate,
# * p: the points earned by both algos defect and
# * s: the points earned by cooperators when their opponents defect.
# Special case: if 'n' is set to NULL, the number of rounds goes random and
# may be anything from 'n.min' to 'n.max'.

match = function(f1, f2, n = 2000, t = 5, r = 3, p = 1, s = 0, n.min = 100,
    n.max = 1000) {
    n1 <- deparse(substitute(f1))
    n2 <- deparse(substitute(f2))
    if(! is(f1, "function") | ! is(f2, "function"))
        stop("'f1' and 'f2' must be functions")
    n <- as.integer(n)
    if(n < 1) stop("'n' must be as least 1")
    if(any(diff(c(t, r, p, s)) >= 0))
        stop("check that: t > r > p > s")
    m <- c(p, s, t, r, p, t, s, r)
    dim(m) <- rep(2, 3)
    s1 <- s2 <- logical(0)
    p1 <- p2 <- integer(0)
    if(is.null(n)) n <- sample(n.min:n.max, 1)
    for(i in 1:n) {
        si1 <- f1(s1, s2, n)[1]
        si2 <- f2(s2, s1, n)[1]
        s1 <- c(s1, si1)
        s2 <- c(s2, si2)
        p1 <- c(p1, m[si1+1, si2+1, 1])
        p2 <- c(p2, m[si1+1, si2+1, 2])
    }
    S <- cbind(s1, s2)
    P <- cbind(p1, p2)
    colnames(S) <- colnames(P) <- c(n1, n2)
    list(S = S, P = P)
}

# -------------------------------------------------------------------------- #
# .tournament
# -------------------------------------------------------------------------- #

# Runs a tournament with algos provided in x, which must be a named list of
# algos. For other arguments, see .match. Returns a matrix with scores of all
# matches. Example:
# res <- .tournament(x, 10) # 10-rounds matches
# rowMeans(res, na.rm = TRUE) # Mean points earned by each algo.

tournament = function(x, n = 2000, t = 5, r = 3, p = 1, s = 0, n.min = 100,
    n.max = 1000) {
    I <- t(combn(N <- length(x), 2))
    nms <- names(x)
    res <- matrix(NA, N, N)
    dimnames(res) <- list(nms, nms)
    for(i in 1:nrow(I)) {
        j <- I[i, 1]
        k <- I[i, 2]
        z <- match(x[[j]], x[[k]], n, t, r, p, s, n.min, n.max)
        v <- unname(colSums(z$P))
        res[j, k] <- v[1]
        res[k, j] <- v[2]
    }
    res
}
```

``` r
# ========================================================================== #
# Testing
# ========================================================================== #


# Run a single 10-rounds match between tft and rand:

(ans <- match(tft, rand, 10))
```

    ## $S
    ##         tft  rand
    ##  [1,]  TRUE FALSE
    ##  [2,] FALSE  TRUE
    ##  [3,]  TRUE  TRUE
    ##  [4,]  TRUE  TRUE
    ##  [5,]  TRUE  TRUE
    ##  [6,]  TRUE FALSE
    ##  [7,] FALSE  TRUE
    ##  [8,]  TRUE FALSE
    ##  [9,] FALSE  TRUE
    ## [10,]  TRUE  TRUE
    ## 
    ## $P
    ##       tft rand
    ##  [1,]   0    5
    ##  [2,]   5    0
    ##  [3,]   3    3
    ##  [4,]   3    3
    ##  [5,]   3    3
    ##  [6,]   0    5
    ##  [7,]   5    0
    ##  [8,]   0    5
    ##  [9,]   5    0
    ## [10,]   3    3

``` r
# That's a list; if you just want the strategies:

ans$S
```

    ##         tft  rand
    ##  [1,]  TRUE FALSE
    ##  [2,] FALSE  TRUE
    ##  [3,]  TRUE  TRUE
    ##  [4,]  TRUE  TRUE
    ##  [5,]  TRUE  TRUE
    ##  [6,]  TRUE FALSE
    ##  [7,] FALSE  TRUE
    ##  [8,]  TRUE FALSE
    ##  [9,] FALSE  TRUE
    ## [10,]  TRUE  TRUE

``` r
# To compute the gains:

colSums(ans$P)
```

    ##  tft rand 
    ##   27   27

``` r
# A small tournament with alld, allc, rand, tft, alt, grudger, detect, gtft,
# wsls and tf2t (200-rounds matches):

x <- list(alld = alld, allc = allc, rand = rand, tft = tft, alt = alt,
    grudger = grudger, detect = detect, gtft = gtft, wsls = wsls,
    tf2t = tf2t)

(ans <- tournament(x, 200))
```

    ##         alld allc rand tft alt grudger detect gtft wsls tf2t
    ## alld      NA 1000  636 204 600     204    212  520  600  208
    ## allc       0   NA  243 600 300     600      9  600  600  600
    ## rand      91  838   NA 501 449     110    107  570  453  621
    ## tft      199  600  501  NA 498     600    599  600  600  600
    ## alt      100  800  434 503  NA     107    503  584  450  800
    ## grudger  199  600  575 600 597      NA    209  600  600  600
    ## detect   197  994  607 599 498     204     NA  599  399  218
    ## gtft     120  600  375 600 444     600    599   NA  600  600
    ## wsls     100  600  438 600 450     600    404  600   NA  600
    ## tf2t     198  600  376 600 300     600    203  600  600   NA

``` r
# Updated: Now .tournament returns a matrix:
sort(rowMeans(ans, na.rm = TRUE))
```

    ##     allc     rand     tf2t     alld      alt   detect     wsls     gtft 
    ## 394.6667 415.5556 453.0000 464.8889 475.6667 479.4444 488.0000 504.2222 
    ##  grudger      tft 
    ## 508.8889 533.0000

``` r
# -------------------------------------------------------------------------- #
# Tit-for-Tat (a.k.a 'Copycat')
# -------------------------------------------------------------------------- #

# Cooperates first then reproduct its opponent's last move.

tft = function(p, o) {
    ifelse(length(p) == 0, TRUE, tail(o, 1))
}

# -------------------------------------------------------------------------- #
# AllD (a.k.a 'Always Cheat')
# -------------------------------------------------------------------------- #

# Allways defects.

alld = function(p, o) {
    FALSE
}

# -------------------------------------------------------------------------- #
# AllC (a.k.a 'Always Cooperate')
# -------------------------------------------------------------------------- #

# Allways cooperates.

allc = function(p, o) {
    TRUE
}

# -------------------------------------------------------------------------- #
# Grudger
# -------------------------------------------------------------------------- #

# Cooperates until its opponent defects; if so, defects forever.

grudger = function(p, o) {
    all(o)
}

# -------------------------------------------------------------------------- #
# Detective
# -------------------------------------------------------------------------- #

# Cooperates, defects, cooperates and cooperates again. If the opponent
# doesn't relatilates in the 3rd round, defects all the time; else, plays
# Tit-for-Tat.

detect = function(p, o) {
    r <- length(p)+1
    if(r < 5) {
        res <- as.logical(r != 2)
    } else {
        res <- ifelse(o[3], FALSE, o[r-1])
    }
    res
}

# -------------------------------------------------------------------------- #
# Tit-For-Two-Tats (a.k.a. 'Copykitten')
# -------------------------------------------------------------------------- #

# Cooperates twice then, only retaliates if the opponent defected twice in a
# row.

tf2t = function(p, o) {
    r <- length(p)+1
    res <- TRUE
    if(r > 2) res <- ! all(! tail(o, 2))
    res
}

# -------------------------------------------------------------------------- #
# Random
# -------------------------------------------------------------------------- #

# Cooperates or defects randomly with a 1/2 chance.

rand = function(p, o) {
    sample(c(T, F), 1)
}

# -------------------------------------------------------------------------- #
# Win-Stay-Lose-Shift (a.k.a 'Simpleton' or 'Pavlov')
# -------------------------------------------------------------------------- #

# If your opponent cooperated with you on the last round, repeat your last
# move; otherwise, switch.

wsls = function(p, o) {
    r <- length(p)+1
    if(r == 1) {
        res <- TRUE
    } else {
        res <- ifelse(tail(o, 1), tail(p, 1), !tail(p, 1))
    }
    res
}

# -------------------------------------------------------------------------- #
# Alternate
# -------------------------------------------------------------------------- #

# Randomly cooperate or defect (prob = 1/2) then alternates regardless of
# what the opponent does.

alt = function(p, o) {
    ifelse(length(p) == 0, sample(c(T, F), 1), !tail(p, 1))
}

# -------------------------------------------------------------------------- #
# Generous Tit-For-Tat
# -------------------------------------------------------------------------- #

# Same as TFT but 'forgives' defections in 1/3 of cases.

gtft = function(p, o) {
    r <- length(p)+1
    res <- TRUE
    if(r > 1 && !tail(o, 1)) {
        res <- sample(c(F, T), 1,2:1/3) 
    }   
    res
}

# -------------------------------------------------------------------------- #
# .match
# -------------------------------------------------------------------------- #

# Runs a n-round match between 'f1' and 'f2' and returns a list with S, a
# matrix of each algo's mooves and P, a matrix containing their respective
# gains.
# You may also modify the payment matrix with :
# * t: the points earned by defectors when their opponents cooperate,
# * r: the points earned by both algos cooperate,
# * p: the points earned by both algos defect and
# * s: the points earned by cooperators when their opponents defect.
# Special case: if 'n' is set to NULL, the number of rounds goes random and
# may be anything from 'n.min' to 'n.max'.

.match = function(f1, f2, n = 200, t = 5, r = 3, p = 1, s = 0, n.min = 100,
    n.max = 1000) {
    n1 <- deparse(substitute(f1))
    n2 <- deparse(substitute(f2))
    if(! is(f1, "function") | ! is(f2, "function"))
        stop("'f1' and 'f2' must be functions")
    n <- as.integer(n)
    if(n < 1) stop("'n' must be as least 1")
    if(any(diff(c(t, r, p, s)) >= 0))
        stop("check that: t > r > p > s")
    m <- c(p, s, t, r, p, t, s, r)
    dim(m) <- rep(2, 3)
    s1 <- s2 <- logical(0)
    p1 <- p2 <- integer(0)
    if(is.null(n)) n <- sample(n.min:n.max, 1)
    for(i in 1:n) {
        si1 <- f1(s1, s2)
        si2 <- f2(s2, s1)
        s1 <- c(s1, si1)
        s2 <- c(s2, si2)
        p1 <- c(p1, m[si1+1, si2+1, 1])
        p2 <- c(p2, m[si1+1, si2+1, 2])
    }
    S <- cbind(s1, s2)
    P <- cbind(p1, p2)
    colnames(S) <- colnames(P) <- c(n1, n2)
    list(S = S, P = P)
}

# -------------------------------------------------------------------------- #
# .tournament
# -------------------------------------------------------------------------- #

# Runs a tournament with algos provided in x, which must be a named list of
# algos. For other arguments, see .match. (You need 'gtools' to run it.)

.tournament = function(x, n = 200, t = 5, r = 3, p = 1, s = 0, n.min = 100,
    n.max = 1000) {
    I <- gtools:::combinations(N <- length(x), 2)
    ans <- list()
    length(ans) <- N
    names(ans) <- names(x)
    for(i in 1:nrow(I)) {
        j <- I[i, 1]
        k <- I[i, 2]
        z <- .match(x[[j]], x[[k]], n, t, r, p, s, n.min, n.max)
        v <- unname(colSums(z$P))
        ans[[j]] <- c(ans[[j]], v[1])
        ans[[k]] <- c(ans[[k]], v[2])
    }
    sapply(ans, mean)
}

# -------------------------------------------------------------------------- #
# .evolution
# -------------------------------------------------------------------------- #

#.evolution = function(x, pop = 1, n = 200, t = 5, r = 3, p = 1,s = 0, n.min = 100, n.max = 1000, cols = NULL, brk = 100) {
    
    if(is.null(cols)) cols <- rainbow(length(x))
    if(length(pop) == 1) pop <- rep(pop, length(x))
    nms <- names(x)
    sv <- ix <- unlist(lapply(1:length(pop), function(i) rep(i, pop[i])))
    X <- x[ix]
    cc <- cols[ix]
    N <- length(X)
    t2xy <- function(t) list(x = cos(-2*pi*t), y = sin(-2*pi*t))
    P <- t2xy(seq(0, 1, len = N))
    op <- par(mar = rep(5, 4))
    lim <- c(-2, 1.5)
    plot(P$x, P$y, cex = 2, axes = FALSE, pch = 16, ylab = NA, xlab = NA,
        xlim = lim, ylim = lim, main = "Init", cex.main = .8,
        col = cc)
    legend(-2, 1.5, legend = names(x), col = cols, pch = 16, cex = .8,
        bty = "n")
    i <- 0
    while(length(unique(ix)) > 1) {
        ans <- .tournament(X, n, t, r, p, s, n.min, n.max)
        si <- order(ans)
        rmv <- tail(si, 5)
        dup <- head(si, 5)
        X[rmv] <- X[dup]
        cc[rmv] <- cc[dup]
        ix[rmv] <- ix[dup]
        plot(P$x, P$y, cex = 2, axes = FALSE, pch = 16, ylab = NA,
            xlab = NA, xlim = lim, ylim = lim,
            main = paste("Stage", i <- i+1),
            cex.main = .8, col = cc)
        legend(-2, 1.5, legend = nms, col = cols, pch = 16,
            cex = .8, bty = "n")
        if(i == brk) break()
    }
    par(op)
    res <- rep(0, length(x))
    names(res) <- nms
    for(i in 1:length(x)) {
        res[i] <- length(ix[ix == i])
    }
    res
#}
```
