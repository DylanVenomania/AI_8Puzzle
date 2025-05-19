# AI_8Puzzle
## 1. Tổng quát
  ### 1.1. Nhóm thuật toán
    Gồm 6 nhóm thuật toán :
      Tìm kiếm không có thông tin: BFS, DFS, IDS, UCS.
      Tìm kiếm có thông tin: Greedy, A*, IDA*
      Tìm kiếm cục bộ: Simple Hill Climbing, Steepest Ascent Hill Climbing, Stochastic Hill Climbing, Beam, Genetic Algorithm
      Tìm kiếm trong môi trường phức tạp: And-Or Search, Belief  (thay thế bằng dynamic ) , Partially
      Tìm kiếm trong môi trường ràng buộc: Kiểm thử, Backtracking, AC-3
      Học tăng cường: Q-Learning
    
  ### 1.2. Mục tiêu 
    Hiểu bản chất của từng nhóm thuật toán, điểm chung, điểm mạnh, điểm yếu của từng nhóm.
    Biết được nhóm nào phù hợp với bài toán gì, có phù hợp với bài toán 8 puzzle không?
    Cũng như giúp nâng cao khả năng áp dụng thuật toán vào các bài toán khác nhau, mặc dù không phù hợp với nó.
    Hiểu rõ và triển khai các thành phần cơ bản của một bài toán tìm kiếm.
    Cung cấp một giao diện đồ họa để tương tác.


## 2. Một số thành phần tỏng project
  ### 2.1 Lớp PuzzleState
    Chức năng: Đại diện cho một trạng thái của bài toán 8-puzzle.
    Các thuộc tính:
      board: Ma trận 3x3, trạng thái hiện tại.
      parent: Trạng thái cha (để truy vết đường đi).
      move: Bước di chuyển để đến trạng thái này.
      cost, depth: Chi phí và độ sâu trong cây tìm kiếm.
      blank_pos: Vị trí ô trống.
    Các phương thức:
      is_goal(goal_state): Kiểm tra trạng thái đích.
      get_possible_moves(): Sinh các trạng thái con.
      get_path(): Truy vết đường đi từ trạng thái đầu đến đây.
      manhattan_distance(goal_state): Heuristic Manhattan.
      __eq__, __hash__, __str__: Hỗ trợ so sánh, in, lưu trong set/dict.
  ### 2.2 Lớp PuzzleSolver
      Chức năng: Chứa tất cả các thuật toán giải 8-puzzle.
      Các thuật toán chính:
        Tìm kiếm vét cạn: bfs, dfs, ucs, ids
        Heuristic: greedy, a_star, ida_star
        Local search: simple_hill_climbing, steepest_ascent_hill_climbing, stochastic_hill_climbing, simulated_annealing
        Metaheuristic: genetic_algorithm, beam_search
        Reinforcement learning: q_learning
        CSP: solve_with_csp (backtracking, forward checking, AC-3)
        AND-OR search: and_or_search
      Các phương thức tiện ích:
        _generate_random_solvable_state: Sinh trạng thái đầu vào hợp lệ.
        solve: Hàm tổng quát, chọn thuật toán theo tên.
  ### 2.3 Lớp/Function mở rộng khác
    PartiallyObservablePuzzle: Mô phỏng môi trường chỉ quan sát được một phần
    DynamicPuzzleEnvironment: Mô phỏng môi trường động, nơi trạng thái hoặc luật chơi có thể thay đổi trong quá trình giải.
  ### 2.4 File: main_gui.py
    Chức năng: Giao diện đồ họa cho phép người dùng:
      Nhập trạng thái đầu/cuối.
      Chọn thuật toán.
      Xem từng bước giải, trạng thái trung gian.
      Tương tác trực tiếp (di chuyển, reset, sinh trạng thái ngẫu nhiên, ...).
    Thành phần chính:
      Class PuzzleGUI: Quản lý toàn bộ giao diện, sự kiện, cập nhật trạng thái.
      Các hàm callback: Xử lý sự kiện nhấn nút, nhập dữ liệu, cập nhật bảng.
  ### 2.4 File: requirements.txt
    Chức năng: Liệt kê các thư viện cần thiết để chạy project, ví dụ: numpy, tkinter, heapq, collections, ...
  ### 2.5 Các thành phần khác
    README.md:  mô tả project, ví dụ, giải thích thuật toán.
    Người dùng nhập trạng thái đầu/cuối qua GUI hoặc code.
    Chọn thuật toán muốn giải 
    Chạy thuật toán: Gọi hàm tương ứng trong PuzzleSolver, truyền vào PuzzleState đầu/cuối.
    Trả về kết quả: Đường đi, số bước, trạng thái trung gian, hoặc thông báo không tìm thấy lời giải.
    Hiển thị kết quả trên GUI hoặc in ra console.
    
## 3. Nội dung
  ### 3.1. Các thuật toán tìm kiếm không có thông tin
  #### BFS: 
    1. Ý tưởng tổng quát của BFS
      BFS là thuật toán tìm kiếm theo chiều rộng.
      BFS duyệt các trạng thái theo cách : xét hết các trạng thái cách trạng thái đầu 1 bước, rồi đến 2 bước, v.v.
      Ưu điểm: Luôn tìm được đường đi ngắn nhất (nếu tồn tại).
      Nhược điểm: Tốn nhiều bộ nhớ vì phải lưu trữ nhiều trạng thái cùng lúc.
    2. Hoạt động
      Khởi tạo
      Đưa trạng thái đầu (initial_state) vào một hàng đợi (queue).
      Tạo một tập (set) để lưu các trạng thái đã duyệt (visited), tránh lặp lại.
      Lặp lại cho đến khi queue rỗng hoặc tìm thấy trạng thái đích:
      Lấy trạng thái đầu tiên ra khỏi queue.
      Nếu trạng thái này là goal_state, truy vết ngược lại qua thuộc tính parent để lấy đường đi và trả về kết quả.
      Nếu chưa phải goal, sinh tất cả các trạng thái con (các bước di chuyển hợp lệ).
      Với mỗi trạng thái con:
      Nếu chưa từng duyệt (không nằm trong visited), thêm vào queue và visited.
      Kết thúc
      Nếu queue rỗng mà chưa tìm thấy goal, trả về "không tìm thấy lời giải".
    3. Giải thích các biến và cấu trúc
      queue: Hàng đợi FIFO, lưu các trạng thái sẽ xét tiếp theo.
      visited: Tập hợp các trạng thái đã duyệt, thường so sánh bằng tuple của board để tiết kiệm bộ nhớ.
      current_state: Trạng thái đang xét ở mỗi vòng lặp.
      goal_state: Trạng thái đích cần tìm.
      parent: Mỗi trạng thái lưu trạng thái cha, giúp truy vết đường đi ngắn nhất sau khi tìm thấy goal.
  
 (https://github.com/DylanVenomania/AI_8Puzzle/raw/main/gifs/bfs.gif)

    
   
    
    
