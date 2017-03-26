## Library Name Here: racket/class

My name: **Xiaoling Zheng**

We decided to create a simple Gomoku game using racket. The libraries we planned to use are racket/gui, math, image, class, etc. We gradually discovered these libraries through out FP 3.

I explored racket/class by creating several classes related to our project (point-class, board-class and game-class).

Gomoku is a 15 by 15 board game with 2 players placing pieces on board in turns; each owns a set of pieces differentiate by color (normally black and white).

Point-class represents one point on the 15 by 15 board. It has coordinates and occupancy status initially set to 'empty, which will be updated later on to 'black ('white) if a black (white) piece is placed on.

```racket
...
(define point-class%
  (class object%
    (init-field x-coord y-coord)
    (init-field (Point (point x-coord y-coord)))
    (init-field (occupancy 'empty))
    (define/public (black-point) (set! occupancy 'black))
    (define/public (white-point) (set! occupancy 'white))
    (super-new)))
...
```

Then, I used math/matrix library to construct a 2-dimensional 15 by 15 array.

```racket
(define (make-board x)
  (build-matrix x x (lambda (a b) (make-object point-class% a b))))
```
Procedure make-board creates a 15 by 15 matrix that each element represents a point-class object holding an assignment coordinate.

(0, 0) ... (0, 14)
(1, 0) ... (1, 14)
..................
(14, 0)... (14, 14)

Board-class holds a board matrix as an init-field and provides procedures to give access to point-class objects in board matrix.

```racket
...
(define board-class%
  (class object%
    (init-field (Dimension square-board-dimension)) ;; dimension from input
    (init-field (BOARD (make-board square-board-dimension))) ;; set up square board with dimension
    (define/public (check-occupancy a b) ;; check occupancy for single point
      (get-field occupancy (matrix-ref BOARD a b)))
    (define/public (set-black a b) ;; set occupany to symbol black
      (send (matrix-ref BOARD a b) black-point))
    (define/public (set-white a b) ;; set occupany to symbol white
      (send (matrix-ref BOARD a b) white-point))
    (super-new)))
...
```
Game-class holds a board-class object, two lists representing two opponents placed location, and procedures that update board-class's board matrix. It only allows basic set black/white piece that both updates board matrix and occupancy list up to this point.
```racket
...
(define game-class%
  (class object%
    (init-field (Board (make-object board-class%)))
    (init-field (Black-points '())) ;; list of occupied black points initialized to null
    (init-field (White-points '())) ;; list of occupied white points initialized to null
    (define/public (check-occupancy x y)
      (send Board check-occupancy x y))
    (define/public (set-black x y) ;; public method both update board and add point to lists
      (send Board set-black x y)
      (set! Black-points (cons (cons x y) Black-points)))
    (define/public (set-white x y) ;; public method both update board and add point to lists
      (send Board set-white x y)
      (set! White-points (cons (cons x y) White-points)))
    ;; .....
    ;; other methods to be added
    ;; .....
    (super-new)))
...
```
Below is a testing output of the classes:

![ClassTest](https://raw.githubusercontent.com/xlzhen/FP3/master/ClassTest.PNG)
