rust-maze-101
==

I asked ChatGPT to write some maze generation code:

```sh
make run
```

```sh
make all
```

* * *

## Example output

```sh
rust-maze-101 % make run      
cargo run 
   Compiling cfg-if v1.0.0
   Compiling ppv-lite86 v0.2.17
   Compiling libc v0.2.153
   Compiling getrandom v0.2.12
   Compiling rand_core v0.6.4
   Compiling rand_chacha v0.3.1
   Compiling rand v0.8.5
   Compiling rust-maze-101 v0.1.0 (/Users/mitch/projects/rust/rust-maze-101)
    Finished dev [unoptimized + debuginfo] target(s) in 0.90s
     Running `target/debug/rust-maze-101`
                  ██      ██          ██              ██                          
  ██████████  ██████  ██  ██  ██  ██████  ██████  ██████  ██████████  ████████████
  ██  ██      ██      ██      ██  ██      ██      ██      ██      ██      ██      
  ██  ██  ██████  ██████████████  ██  ██████  ██████  ██████████  ██████  ██  ██  
  ██  ██  ██      ██  ██      ██          ██  ██      ██              ██      ██  
  ██  ██  ██  ██████  ██  ██  ██████████████  ██  ██████  ██  ██████████████████  
      ██      ██      ██  ██              ██  ██  ██      ██  ██              ██  
████  ██████████  ██  ██  ██████████████  ██  ██  ██████████  ██  ██████████  ██  
      ██          ██  ██      ██              ██  ██          ██  ██          ██  
  ██████  ██████████  ██████  ██████████████████  ██  ██████  ██  ██████████  ██  
      ██      ██  ██  ██      ██                      ██      ██          ██      
████  ██████  ██  ██  ██  ██████  ██████████████████████████████████  ██  ████████
  ██      ██  ██      ██      ██  ██          ██      ██          ██  ██  ██      
  ██████  ██  ██████████████  ██  ██  ██████████  ██  ██  ██████  ██████  ██  ██  
      ██  ██              ██      ██  ██      ██  ██  ██  ██  ██          ██  ██  
  ██  ██  ██  ██████  ██████████████  ██  ██  ██████  ██  ██  ██████████████  ██  
  ██      ██      ██                      ██      ██      ██                  ██  
  ██████████  ██████████  ██████████████████████  ██████████████████████  ██████  
  ██      ██  ██      ██                      ██      ██                  ██  ██  
  ██  ██  ██████  ██  ██████████████████████████  ██  ██  ██████████████████  ██  
      ██          ██                              ██      ██                      
mitch@desert-air rust-maze-101 % 

```

* * *

## ChatGPT Conversation

### Prompt

```sh
write a program in rust to generate a maze
```

### ChatGPT Response

Generating a maze programmatically can be a fun and interesting project. In Rust, we can use one of the many algorithms for maze generation, such as the Depth-first search (DFS) algorithm, which is relatively straightforward to implement. Below is a simple Rust program that generates a maze using the DFS algorithm.

This program will create a maze of a specified size, represented as a grid of cells. Each cell can either be a wall or a path. The maze generation starts from a random cell and then recursively visits neighboring cells, carving out a path and avoiding revisiting cells.

First, ensure you have Rust installed on your machine. You can download it from the official Rust website or use rustup.

### Maze Generator in Rust

```rust
use rand::prelude::*;
use std::collections::VecDeque;

#[derive(Clone, Copy, Debug, PartialEq)]
enum Cell {
    Wall,
    Path,
}

#[derive(Clone, Copy, Debug)]
struct Point {
    x: usize,
    y: usize,
}

impl Point {
    fn new(x: usize, y: usize) -> Self {
        Point { x, y }
    }
}

fn generate_maze(width: usize, height: usize) -> Vec<Vec<Cell>> {
    let mut maze = vec![vec![Cell::Wall; width]; height];
    let mut stack = VecDeque::new();
    let mut rng = thread_rng();

    // Random starting point
    let start = Point::new(rng.gen_range(0..width / 2) * 2, rng.gen_range(0..height / 2) * 2);
    maze[start.y][start.x] = Cell::Path;
    stack.push_back(start);

    while let Some(current) = stack.pop_back() {
        let mut neighbors = Vec::new();

        // Find all possible neighbors
        for &(dx, dy) in &[(0, 2), (2, 0), (0, -2), (-2, 0)] {
            let nx = current.x as isize + dx;
            let ny = current.y as isize + dy;

            if nx >= 0 && nx < width as isize && ny >= 0 && ny < height as isize {
                let nx = nx as usize;
                let ny = ny as usize;
                if maze[ny][nx] == Cell::Wall {
                    neighbors.push(Point::new(nx, ny));
                }
            }
        }

        if !neighbors.is_empty() {
            stack.push_back(current);

            let chosen = neighbors[rng.gen_range(0..neighbors.len())];
            maze[chosen.y][chosen.x] = Cell::Path;

            // Knock down the wall between
            maze[(chosen.y + current.y) / 2][(chosen.x + current.x) / 2] = Cell::Path;

            stack.push_back(chosen);
        }
    }

    maze
}

fn print_maze(maze: &[Vec<Cell>]) {
    for row in maze {
        for &cell in row {
            print!("{}", if cell == Cell::Path { "  " } else { "██" });
        }
        println!();
    }
}

fn main() {
    let width = 41; // Should be odd
    let height = 21; // Should be odd
    let maze = generate_maze(width, height);
    print_maze(&maze);
}
```

### Steps to Run the Program

1. **Create a New Rust Project**: Use `cargo new maze_generator` to create a new Rust project.
2. **Add Dependencies**: Open `Cargo.toml` and add `rand` to your dependencies.
   ```toml
   [dependencies]
   rand = "0.8.3"
   ```
3. **Write the Code**: Copy the provided Rust code into the `main.rs` file of your project.
4. **Build and Run**: Use `cargo run` in your terminal within the project directory to compile and execute the program.

This program generates a simple maze and prints it to the console using ASCII characters. You can adjust the `width` and `height` variables in the `main` function to change the size of the generated maze. Remember, for the algorithm to work correctly, both the width and height should be odd numbers.
