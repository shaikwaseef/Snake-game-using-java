# Snake-game-using-java
In our childhood, nearly all of us enjoyed playing classic snake games. Now we will try to enhance it with the help of Java concepts. The concept appears to be easy but it is not that effortless to implement.
Creating an enhanced version of the classic snake game in Java can be an excellent project to improve your understanding of Java concepts such as object-oriented programming, threading, event handling, and graphics. Below is an outline and a basic implementation plan to get you started.

### Outline
1. **Setup the Game Window:**
   - Use `JFrame` to create the main window.
   - Use `JPanel` for the game board.

2. **Game Components:**
   - **Snake:** Represented by a list of `Point` objects.
   - **Food:** Represented by a single `Point`.
   - **Direction:** Enum to handle the direction of the snake (UP, DOWN, LEFT, RIGHT).

3. **Game Logic:**
   - Initialize the snake and food positions.
   - Handle keyboard input to change the direction of the snake.
   - Implement the game loop using a timer.
   - Check for collisions (with the walls, the snake itself, or the food).

4. **Rendering:**
   - Override the `paintComponent` method in `JPanel` to draw the snake, food, and other game elements.

5. **Enhancements:**
   - Add scoring.
   - Increase the speed as the game progresses.
   - Add levels or obstacles.

### Implementation

#### 1. Setup the Game Window
```java
import javax.swing.JFrame;

public class SnakeGame extends JFrame {
    public SnakeGame() {
        initUI();
    }

    private void initUI() {
        add(new GameBoard());
        setResizable(false);
        pack();
        
        setTitle("Snake Game");
        setLocationRelativeTo(null);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
    }

    public static void main(String[] args) {
        javax.swing.SwingUtilities.invokeLater(() -> {
            JFrame ex = new SnakeGame();
            ex.setVisible(true);
        });
    }
}
```

#### 2. Game Components and Logic

```java
import javax.swing.JPanel;
import java.awt.Color;
import java.awt.Dimension;
import java.awt.Font;
import java.awt.FontMetrics;
import java.awt.Graphics;
import java.awt.Point;
import java.awt.Toolkit;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.awt.event.KeyAdapter;
import java.awt.event.KeyEvent;
import java.util.ArrayList;
import java.util.List;
import javax.swing.Timer;

public class GameBoard extends JPanel implements ActionListener {

    private final int B_WIDTH = 300;
    private final int B_HEIGHT = 300;
    private final int DOT_SIZE = 10;
    private final int ALL_DOTS = 900;
    private final int RAND_POS = 29;
    private final int DELAY = 140;

    private final List<Point> snake = new ArrayList<>();
    private Point food;
    private boolean inGame = true;

    private Timer timer;
    private Direction direction = Direction.RIGHT;

    public GameBoard() {
        initBoard();
    }

    private void initBoard() {
        addKeyListener(new TAdapter());
        setBackground(Color.black);
        setFocusable(true);

        setPreferredSize(new Dimension(B_WIDTH, B_HEIGHT));
        loadGame();
    }

    private void loadGame() {
        for (int i = 0; i < 3; i++) {
            snake.add(new Point(50 - i * DOT_SIZE, 50));
        }

        locateFood();

        timer = new Timer(DELAY, this);
        timer.start();
    }

    private void locateFood() {
        int r = (int) (Math.random() * RAND_POS);
        int foodX = ((r * DOT_SIZE));

        r = (int) (Math.random() * RAND_POS);
        int foodY = ((r * DOT_SIZE));

        food = new Point(foodX, foodY);
    }

    @Override
    public void paintComponent(Graphics g) {
        super.paintComponent(g);

        if (inGame) {
            drawObjects(g);
        } else {
            gameOver(g);
        }

        Toolkit.getDefaultToolkit().sync();
    }

    private void drawObjects(Graphics g) {
        g.setColor(Color.red);
        g.fillRect(food.x, food.y, DOT_SIZE, DOT_SIZE);

        for (Point dot : snake) {
            g.setColor(Color.green);
            g.fillRect(dot.x, dot.y, DOT_SIZE, DOT_SIZE);
        }
    }

    private void gameOver(Graphics g) {
        String msg = "Game Over";
        Font small = new Font("Helvetica", Font.BOLD, 14);
        FontMetrics metr = getFontMetrics(small);

        g.setColor(Color.white);
        g.setFont(small);
        g.drawString(msg, (B_WIDTH - metr.stringWidth(msg)) / 2, B_HEIGHT / 2);
    }

    private void checkCollision() {
        for (int z = snake.size(); z > 0; z--) {
            if ((z > 4) && (snake.get(0).equals(snake.get(z)))) {
                inGame = false;
            }
        }

        if (snake.get(0).x >= B_WIDTH || snake.get(0).x < 0 || snake.get(0).y >= B_HEIGHT || snake.get(0).y < 0) {
            inGame = false;
        }

        if (!inGame) {
            timer.stop();
        }
    }

    private void checkFood() {
        if (snake.get(0).equals(food)) {
            snake.add(new Point(-1, -1));
            locateFood();
        }
    }

    private void move() {
        for (int z = snake.size() - 1; z > 0; z--) {
            snake.set(z, new Point(snake.get(z - 1)));
        }

        switch (direction) {
            case LEFT -> snake.get(0).translate(-DOT_SIZE, 0);
            case RIGHT -> snake.get(0).translate(DOT_SIZE, 0);
            case UP -> snake.get(0).translate(0, -DOT_SIZE);
            case DOWN -> snake.get(0).translate(0, DOT_SIZE);
        }
    }

    @Override
    public void actionPerformed(ActionEvent e) {
        if (inGame) {
            checkFood();
            checkCollision();
            move();
        }

        repaint();
    }

    private class TAdapter extends KeyAdapter {
        @Override
        public void keyPressed(KeyEvent e) {
            int key = e.getKeyCode();

            switch (key) {
                case KeyEvent.VK_LEFT -> {
                    if (direction != Direction.RIGHT) {
                        direction = Direction.LEFT;
                    }
                }
                case KeyEvent.VK_RIGHT -> {
                    if (direction != Direction.LEFT) {
                        direction = Direction.RIGHT;
                    }
                }
                case KeyEvent.VK_UP -> {
                    if (direction != Direction.DOWN) {
                        direction = Direction.UP;
                    }
                }
                case KeyEvent.VK_DOWN -> {
                    if (direction != Direction.UP) {
                        direction = Direction.DOWN;
                    }
                }
            }
        }
    }

    private enum Direction {
        LEFT, RIGHT, UP, DOWN
    }
}
```

### Explanation
1. **Setup the Game Window:**
   - `SnakeGame` extends `JFrame` and sets up the main game window.
   - `GameBoard` extends `JPanel` and manages the game components and rendering.

2. **Game Components:**
   - The snake is represented by a list of `Point` objects.
   - The food is a single `Point`.

3. **Game Logic:**
   - Initializes the snake with three segments.
   - Uses `Timer` for the game loop, calling `actionPerformed` periodically.
   - `TAdapter` handles keyboard input to change the direction of the snake.

4. **Rendering:**
   - `paintComponent` is overridden to draw the snake and the food.
   - `drawObjects` and `gameOver` methods handle the rendering of game elements.

5. **Game Mechanics:**
   - `checkCollision` checks if the snake has hit the walls or itself.
   - `checkFood` checks if the snake has eaten the food.
   - `move` updates the position of the snake based on the current direction.

This basic implementation provides a solid foundation for the classic snake game. You can further enhance it by adding more features such as scoring, increasing difficulty, and adding obstacles or levels.
