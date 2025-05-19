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
      Khởi tạo:
      Đưa trạng thái đầu (initial_state) vào một hàng đợi (queue).
      Tạo một tập (set) để lưu các trạng thái đã duyệt (visited), tránh lặp lại.
      
      Lặp lại cho đến khi queue rỗng hoặc tìm thấy trạng thái đích:
        Lấy trạng thái đầu tiên ra khỏi queue.
        Nếu trạng thái này là goal_state, truy vết ngược lại qua thuộc tính parent để lấy đường đi và trả về kết quả.
        Nếu chưa phải goal, sinh tất cả các trạng thái con (các bước di chuyển hợp lệ).
      Với mỗi trạng thái con:
        Nếu chưa từng duyệt (không nằm trong visited), thêm vào queue và visited.
      
      Kết thúc:
        Nếu queue rỗng mà chưa tìm thấy goal, trả về "không tìm thấy lời giải".
    3. Giải thích các biến và cấu trúc
      queue: Hàng đợi FIFO, lưu các trạng thái sẽ xét tiếp theo.
      visited: Tập hợp các trạng thái đã duyệt, thường so sánh bằng tuple của board để tiết kiệm bộ nhớ.
      current_state: Trạng thái đang xét ở mỗi vòng lặp.
      goal_state: Trạng thái đích cần tìm.
      parent: Mỗi trạng thái lưu trạng thái cha, giúp truy vết đường đi ngắn nhất sau khi tìm thấy goal.
    4. Ưu nhược điểm của BFS trong 8-puzzle
      Ưu điểm:
        Đảm bảo tìm ra đường đi ngắn nhất (ít bước nhất).
        Dễ cài đặt, dễ hiểu.
      Nhược điểm:
        Tốn nhiều RAM khi trạng thái đầu cách đích nhiều bước.
        Không thích hợp cho các bài toán có không gian trạng thái quá lớn.
  
  ![BFS Animation for 8 Puzzle](https://github.com/DylanVenomania/AI_8Puzzle/raw/main/gifs/bfs.gif)
  
  #### DFS: 
    1. Ý tưởng tổng quát của DFS
      DFS là thuật toán tìm kiếm theo chiều sâu.
      DFS duyệt các trạng thái bằng cách đi sâu nhất có thể vào một nhánh trước, chỉ khi không còn đi tiếp mới quay lại để thử nhánh khác.
      Ưu điểm: Tốn ít bộ nhớ hơn so với BFS, vì chỉ cần lưu đường đi hiện tại và các nhánh đang mở rộng.
      Nhược điểm: Không đảm bảo tìm được đường đi ngắn nhất, dễ bị lặp vô hạn nếu không kiểm soát, có thể bị kẹt ở nhánh không có lời giải.
    2. Hoạt động
      Khởi tạo :
        Đưa trạng thái đầu (initial_state) vào một ngăn xếp (stack).
        Tạo một tập (visited) để lưu các trạng thái đã duyệt, tránh lặp lại.
      Lặp lại cho đến khi stack rỗng hoặc tìm thấy trạng thái đích:
        Lấy trạng thái trên cùng ra khỏi stack.
        Nếu trạng thái này là goal_state, truy vết ngược lại qua thuộc tính parent để lấy đường đi và trả về kết quả.
        Nếu chưa phải goal, sinh tất cả các trạng thái con (các bước di chuyển hợp lệ).
        Với mỗi trạng thái con:
        Nếu chưa từng duyệt (không nằm trong visited) và chưa vượt quá giới hạn độ sâu (nếu có), thêm vào stack và visited.
      Kết thúc :
        Nếu stack rỗng mà chưa tìm thấy goal, trả về "không tìm thấy lời giải".
    3. Giải thích các biến và cấu trúc
      stack: Ngăn xếp LIFO, lưu các trạng thái sẽ xét tiếp theo.
      visited: Tập hợp các trạng thái đã duyệt, thường lưu dưới dạng tuple của board để tiết kiệm bộ nhớ và so sánh nhanh.
      current_state: Trạng thái đang xét ở mỗi vòng lặp.
      goal_state: Trạng thái đích cần tìm.
      parent: Mỗi trạng thái lưu trạng thái cha, giúp truy vết đường đi nếu tìm thấy goal.
      depth: Độ sâu hiện tại của trạng thái (dùng để giới hạn độ sâu nếu cần).
    4. Ưu nhược điểm của DFS trong 8-puzzle
      Ưu điểm:
        Tốn ít RAM hơn BFS, vì chỉ cần lưu đường đi hiện tại và các nhánh đang mở rộng.
        Có thể tìm ra lời giải nhanh nếu lời giải nằm ở nhánh sâu ưu tiên.
        Cài đặt đơn giản, dễ hiểu.
      Nhược điểm:
        Không đảm bảo tìm được đường đi ngắn nhất (có thể trả về lời giải rất dài).
        Dễ bị lặp vô hạn nếu không kiểm soát visited hoặc giới hạn độ sâu.
        Nếu lời giải nằm ở nhánh khác, DFS có thể phải duyệt rất nhiều trạng thái vô ích trước khi tìm thấy.
        Không phù hợp với các bài toán có không gian trạng thái quá lớn hoặc cần lời giải tối ưu.

   ![DFS Animation for 8 Puzzle](https://github.com/DylanVenomania/AI_8Puzzle/raw/main/gifs/dfs.gif)

  ### UCS
    1. Ý tưởng tổng quát của UCS
      UCS là thuật toán tìm kiếm theo chi phí đều (Uniform Cost Search).
      UCS luôn mở rộng trạng thái có tổng chi phí nhỏ nhất từ trạng thái đầu đến trạng thái hiện tại (cost g(n)), không quan tâm đến heuristic.
      Ưu điểm: Đảm bảo tìm được đường đi có tổng chi phí nhỏ nhất (tối ưu), nếu mọi bước đi đều có cùng chi phí thì UCS giống BFS.
      Nhược điểm: Nếu không gian trạng thái lớn hoặc nhiều trạng thái có cùng chi phí, UCS vẫn có thể tốn nhiều bộ nhớ và thời gian.
    2. Hoạt động
      Khởi tạo
        Đưa trạng thái đầu (initial_state) vào một hàng đợi ưu tiên (priority queue), với chi phí là 0.
        Tạo một dictionary (cost_so_far) để lưu chi phí nhỏ nhất đã biết đến từng trạng thái.
        Tạo một tập (visited) để lưu các trạng thái đã duyệt.
        
      Lặp lại cho đến khi queue rỗng hoặc tìm thấy trạng thái đích:
        Lấy trạng thái có chi phí nhỏ nhất ra khỏi priority queue.
        Nếu trạng thái này là goal_state, truy vết ngược lại qua thuộc tính parent để lấy đường đi và trả về kết quả.
        Nếu chưa phải goal, sinh tất cả các trạng thái con (các bước di chuyển hợp lệ).
        Với mỗi trạng thái con:
          Tính tổng chi phí đến trạng thái con.
          Nếu trạng thái con chưa từng duyệt, hoặc có chi phí tốt hơn trước đó, cập nhật chi phí, thêm vào queue và visited.
      Kết thúc :
        Nếu queue rỗng mà chưa tìm thấy goal, trả về "không tìm thấy lời giải".
    3. Giải thích các biến và cấu trúc
        priority queue (frontier): Hàng đợi ưu tiên, luôn lấy trạng thái có chi phí nhỏ nhất để mở rộng tiếp theo.
        cost_so_far: Dictionary lưu chi phí nhỏ nhất đã biết đến từng trạng thái.
        visited: Tập hợp các trạng thái đã duyệt, giúp tránh lặp lại.
        current_state: Trạng thái đang xét ở mỗi vòng lặp.
        goal_state: Trạng thái đích cần tìm.
        parent: Mỗi trạng thái lưu trạng thái cha, giúp truy vết đường đi ngắn nhất sau khi tìm thấy goal.
        cost: Tổng chi phí từ trạng thái đầu đến trạng thái hiện tại (g(n)).
    4. Ưu nhược điểm của UCS trong 8-puzzle
      Ưu điểm:
        Đảm bảo tìm ra đường đi có tổng chi phí nhỏ nhất (tối ưu tuyệt đối nếu chi phí mỗi bước là như nhau).
        Không bị ảnh hưởng bởi heuristic kém như Greedy.
      Nhược điểm:
        Nếu mọi bước đi đều có cùng chi phí, UCS sẽ duyệt rất nhiều trạng thái giống BFS.
        Tốn nhiều RAM và thời gian khi trạng thái đầu cách đích nhiều bước hoặc không gian trạng thái lớn.
        Không tận dụng được thông tin heuristic nên có thể chậm hơn A* trong nhiều trường hợp.
  ![UCS Animation](https://github.com/DylanVenomania/AI_8Puzzle/raw/main/gifs/ucs.gif)
  
  ### IDS
    1. Ý tưởng tổng quát của IDS
      IDS là thuật toán tìm kiếm sâu dần (Iterative Deepening Search).
      IDS kết hợp ưu điểm của DFS (tiết kiệm bộ nhớ) và BFS (tìm đường đi ngắn nhất).
      Thuật toán lặp lại nhiều lần, mỗi lần thực hiện DFS với giới hạn độ sâu tăng dần (depth limit). Bắt đầu từ độ sâu nhỏ nhất, tăng dần cho đến khi tìm thấy lời giải hoặc đạt giới hạn tối đa.
      Ưu điểm: Đảm bảo tìm được đường đi ngắn nhất, tiết kiệm bộ nhớ hơn BFS.
      Nhược điểm: Có thể duyệt lại nhiều trạng thái ở các lần lặp khác nhau, dẫn đến tổng số trạng thái duyệt lớn hơn BFS.
    2. Hoạt động
      Khởi tạo
        Đặt depth limit ban đầu là 0.
      Lặp lại cho đến khi tìm thấy lời giải hoặc đạt giới hạn độ sâu tối đa:
        Thực hiện DFS với giới hạn độ sâu hiện tại (chỉ đi sâu tối đa đến depth limit).
        Nếu tìm thấy goal_state, truy vết ngược lại qua thuộc tính parent để lấy đường đi và trả về kết quả.
        Nếu chưa tìm thấy, tăng depth limit lên 1 và lặp lại.
      Kết thúc
        Nếu đã thử đến độ sâu tối đa mà không tìm thấy goal, trả về "không tìm thấy lời giải".
    3. Giải thích các biến và cấu trúc
      depth_limit: Độ sâu tối đa cho phép ở mỗi lần lặp.
      DFS stack: Ngăn xếp dùng cho DFS ở mỗi lần lặp, chỉ đi sâu đến depth_limit.
      visited: Có thể không dùng hoặc chỉ dùng trong mỗi lần DFS, vì IDS cho phép duyệt lại trạng thái ở các lần lặp khác nhau.
      current_state: Trạng thái đang xét ở mỗi vòng lặp.
      goal_state: Trạng thái đích cần tìm.
      parent: Mỗi trạng thái lưu trạng thái cha, giúp truy vết đường đi nếu tìm thấy goal.
      depth: Độ sâu hiện tại của trạng thái (so sánh với depth_limit).
    4. Ưu nhược điểm của IDS trong 8-puzzle
    Ưu điểm:
      Đảm bảo tìm ra đường đi ngắn nhất (giống BFS).
      Tiết kiệm bộ nhớ hơn BFS (giống DFS), vì chỉ cần lưu đường đi hiện tại ở mỗi lần DFS.
      Không bị kẹt ở các nhánh sâu vô ích như DFS thuần túy.
    Nhược điểm:
      Duyệt lại nhiều trạng thái ở các lần lặp khác nhau, tổng số trạng thái duyệt có thể lớn hơn BFS.
      Chạy chậm hơn BFS nếu lời giải ở độ sâu lớn, do phải lặp lại nhiều lần DFS với depth limit tăng dần.
      Không phù hợp với bài toán có không gian trạng thái quá lớn hoặc độ sâu lời giải quá lớn.
  ![IDS Animation](https://github.com/DylanVenomania/AI_8Puzzle/raw/main/gifs/ids.gif)
  
  ### Greedy
    1. Ý tưởng tổng quát của Greedy
        Greedy Best-First Search là thuật toán tìm kiếm theo hướng tham lam.
        Ở mỗi bước, thuật toán luôn chọn trạng thái có giá trị heuristic nhỏ nhất (dùng khoảng cách Manhattan).
        Không quan tâm đến chi phí đã đi (g(n)), chỉ quan tâm đến ước lượng còn lại (h(n)).
        Ưu điểm: Tốc độ nhanh, dễ cài đặt, thường tìm được lời giải nhanh nếu heuristic tốt.
        Nhược điểm: Không đảm bảo tìm được đường đi ngắn nhất, có thể bị kẹt ở local optimum hoặc đi vòng vèo.
    2. Hoạt động
      Khởi tạo :
        Đưa trạng thái đầu (initial_state) vào một hàng đợi ưu tiên (priority queue), với giá trị heuristic (h(n)).
        Tạo một tập (visited) để lưu các trạng thái đã duyệt, tránh lặp lại.
      Lặp lại cho đến khi queue rỗng hoặc tìm thấy trạng thái đích:
        Lấy trạng thái có heuristic nhỏ nhất ra khỏi priority queue.
        Nếu trạng thái này là goal_state, truy vết ngược lại qua thuộc tính parent để lấy đường đi và trả về kết quả.
        Nếu chưa phải goal, sinh tất cả các trạng thái con (các bước di chuyển hợp lệ).
        Với mỗi trạng thái con:
        Tính heuristic cho trạng thái con.
        Nếu chưa từng duyệt (không nằm trong visited), thêm vào queue và visited.
      Kết thúc
        Nếu queue rỗng mà chưa tìm thấy goal, trả về "không tìm thấy lời giải".
    3. Giải thích các biến và cấu trúc
      priority queue (frontier): Hàng đợi ưu tiên, luôn lấy trạng thái có giá trị heuristic nhỏ nhất để mở rộng tiếp theo.
      heuristic (h(n)): Hàm đánh giá độ gần với trạng thái đích (thường dùng tổng khoảng cách Manhattan).
      visited: Tập hợp các trạng thái đã duyệt, giúp tránh lặp lại.
      current_state: Trạng thái đang xét ở mỗi vòng lặp.
      goal_state: Trạng thái đích cần tìm.
      parent: Mỗi trạng thái lưu trạng thái cha, giúp truy vết đường đi nếu tìm thấy goal.
    4. Ưu nhược điểm của Greedy trong 8-puzzle
      Ưu điểm:
        Tốc độ rất nhanh khi heuristic tốt, phù hợp cho trạng thái đầu gần đích.
        Cài đặt đơn giản, dễ hiểu.
        Ít tốn bộ nhớ hơn BFS/UCS/A* 
      Nhược điểm:
        Không đảm bảo tìm được đường đi ngắn nhất, có thể trả về đường đi dài hoặc không tối ưu.
        Có thể bị kẹt ở local optimum (trạng thái tưởng là tốt nhất nhưng không dẫn đến đích).
        
  ![Greedy Animation](https://github.com/DylanVenomania/AI_8Puzzle/raw/main/gifs/greedy.gif)
  ### A*
    1. Ý tưởng tổng quát của A*
        A* là thuật toán tìm kiếm kết hợp giữa chi phí thực tế đã đi (g(n)) và ước lượng chi phí còn lại đến đích (h(n)), theo công thức:
        f(n) = g(n) + h(n)
        Ở mỗi bước, thuật toán luôn chọn trạng thái có giá trị f(n) nhỏ nhất để mở rộng.
        Nếu heuristic h(n) là admissible (không vượt quá chi phí thực sự đến đích), A* đảm bảo tìm được đường đi ngắn nhất.
        Ưu điểm: Tìm đường đi tối ưu, tận dụng cả chi phí thực lẫn thông tin heuristic.
        Nhược điểm: Tốn nhiều bộ nhớ nếu không gian trạng thái lớn, phụ thuộc vào chất lượng heuristic.
      2. Hoạt động
        Khởi tạo
          Đưa trạng thái đầu (initial_state) vào một hàng đợi ưu tiên (priority queue), với f(n) = g(n) + h(n) (g(n) = 0 ở trạng thái đầu).
          Tạo một dictionary (g_scores) để lưu chi phí nhỏ nhất đã biết từ đầu đến từng trạng thái.
          Tạo một tập (visited) để lưu các trạng thái đã duyệt.
          
        Lặp lại cho đến khi queue rỗng hoặc tìm thấy trạng thái đích:
          Lấy trạng thái có f(n) nhỏ nhất ra khỏi priority queue.
          Nếu trạng thái này là goal_state, truy vết ngược lại qua thuộc tính parent để lấy đường đi và trả về kết quả.
          Nếu chưa phải goal, sinh tất cả các trạng thái con (các bước di chuyển hợp lệ).
          Với mỗi trạng thái con:
          Tính g(n) mới (chi phí từ đầu đến trạng thái con).
          Tính h(n) (heuristic, thường là tổng khoảng cách Manhattan).
          Tính f(n) = g(n) + h(n).
          Nếu trạng thái con chưa từng duyệt, hoặc có g(n) tốt hơn trước đó, cập nhật g_scores, thêm vào queue và visited.
        Kết thúc
          Nếu queue rỗng mà chưa tìm thấy goal, trả về "không tìm thấy lời giải".
      3. Giải thích các biến và cấu trúc
        priority queue (frontier): Hàng đợi ưu tiên, luôn lấy trạng thái có f(n) nhỏ nhất để mở rộng tiếp theo.
        g(n): Chi phí thực tế từ trạng thái đầu đến trạng thái hiện tại.
        h(n): Heuristic (ước lượng chi phí còn lại đến đích, thường là tổng khoảng cách Manhattan).
        f(n): Tổng chi phí dự đoán từ đầu đến đích qua trạng thái hiện tại (g(n) + h(n)).
        g_scores: Dictionary lưu g(n) nhỏ nhất đã biết cho từng trạng thái.
        visited: Tập hợp các trạng thái đã duyệt, giúp tránh lặp lại.
        current_state: Trạng thái đang xét ở mỗi vòng lặp.
        goal_state: Trạng thái đích cần tìm.
        parent: Mỗi trạng thái lưu trạng thái cha, giúp truy vết đường đi tối ưu sau khi tìm thấy goal.
      4. Ưu nhược điểm của A* trong 8-puzzle
        Ưu điểm:    
          Đảm bảo tìm ra đường đi tối ưu (ngắn nhất) nếu heuristic tốt (admissible).
          Hiệu quả hơn BFS/UCS khi heuristic tốt, vì ưu tiên mở rộng các trạng thái gần đích.
          Có thể điều chỉnh hiệu quả bằng cách chọn heuristic phù hợp.
        Nhược điểm:
          Tốn nhiều RAM nếu không gian trạng thái lớn hoặc lời giải ở xa.
          Nếu heuristic không tốt, có thể duyệt nhiều trạng thái không cần thiết.
          Cài đặt phức tạp hơn BFS/DFS/Greedy một chút do phải quản lý cả g(n), h(n), f(n).
  ![A Star Animation](https://github.com/DylanVenomania/AI_8Puzzle/raw/main/gifs/A_star.gif)
  ### IDA*
    1. Ý tưởng tổng quát của IDA*
      IDA* là thuật toán kết hợp giữa A* và DFS sâu dần.
      Thay vì lưu toàn bộ các trạng thái như A*, IDA* sử dụng DFS nhưng chỉ mở rộng các trạng thái có f(n) ≤ một ngưỡng (bound) nhất định.
      Mỗi lần lặp, ngưỡng bound sẽ tăng lên bằng giá trị f(n) nhỏ nhất vừa vượt quá bound trước đó.
      Ưu điểm: Tiết kiệm bộ nhớ hơn A*, vẫn đảm bảo tìm được đường đi tối ưu nếu heuristic tốt.
      Nhược điểm: Có thể duyệt lại nhiều trạng thái ở các lần lặp khác nhau (giống IDS), tổng số trạng thái duyệt có thể lớn.
    2. Hoạt động
    Khởi tạo
      Tính f(n) = g(n) + h(n) cho trạng thái đầu (initial_state), đặt bound ban đầu là f(n) này.
    Lặp lại cho đến khi tìm thấy lời giải hoặc không còn trạng thái hợp lệ:
      Thực hiện DFS, chỉ mở rộng các trạng thái có f(n) ≤ bound hiện tại.
      Nếu gặp trạng thái đích (goal_state) trong quá trình DFS, truy vết ngược lại qua thuộc tính parent để lấy đường đi và trả về kết quả.
      Nếu không tìm thấy, lấy giá trị f(n) nhỏ nhất vừa vượt quá bound hiện tại, đặt làm bound mới và lặp lại.
    Kết thúc
      Nếu không còn trạng thái nào hợp lệ mà chưa tìm thấy goal, trả về "không tìm thấy lời giải".
    3. Giải thích các biến và cấu trúc
      bound: Ngưỡng f(n) tối đa cho phép ở mỗi lần lặp (ban đầu là f(initial_state)).
      DFS stack: Ngăn xếp dùng cho DFS ở mỗi lần lặp, chỉ mở rộng trạng thái có f(n) ≤ bound.
      f(n): Tổng chi phí dự đoán từ đầu đến đích qua trạng thái hiện tại (g(n) + h(n)).
      g(n): Chi phí thực tế từ trạng thái đầu đến trạng thái hiện tại.
      h(n): Heuristic (ước lượng chi phí còn lại đến đích, thường là tổng khoảng cách Manhattan).
      min_over_bound: Giá trị f(n) nhỏ nhất vừa vượt quá bound trong lần lặp hiện tại, dùng để cập nhật bound cho lần lặp sau.
      current_state: Trạng thái đang xét ở mỗi vòng lặp.
      goal_state: Trạng thái đích cần tìm.
      parent: Mỗi trạng thái lưu trạng thái cha, giúp truy vết đường đi tối ưu sau khi tìm thấy goal.
    4. Ưu nhược điểm của IDA* trong 8-puzzle
      Ưu điểm:
        Tiết kiệm bộ nhớ hơn A* rất nhiều, vì chỉ cần lưu đường đi hiện tại (giống DFS).
        Đảm bảo tìm ra đường đi tối ưu (nếu heuristic tốt).
        Phù hợp với các bài toán có không gian trạng thái lớn mà A* không đủ RAM để chạy.
      Nhược điểm:
        Duyệt lại nhiều trạng thái ở các lần lặp khác nhau, tổng số trạng thái duyệt có thể lớn hơn A*.
        Chạy chậm hơn A* nếu lời giải ở độ sâu lớn hoặc heuristic không tốt.
        Cài đặt phức tạp hơn so với BFS/DFS/A*.
  
  ![IDA Star Animation](https://github.com/DylanVenomania/AI_8Puzzle/raw/main/gifs/IDA_star.gif)
  
  ### Simple hill climbing 
    1. Ý tưởng tổng quát của Simple Hill Climbing
      Simple Hill Climbing là thuật toán tìm kiếm cục bộ (local search).
      Ở mỗi bước, thuật toán luôn chọn trạng thái láng giềng (neighbor) tốt hơn hiện tại, dựa trên giá trị heuristic (thường là tổng khoảng cách Manhattan đến đích).
      Nếu không còn láng giềng nào tốt hơn, thuật toán dừng lại (có thể rơi vào local optimum).
      Ưu điểm: Đơn giản, dễ cài đặt, chạy nhanh với trạng thái đầu gần đích.
      Nhược điểm: Dễ bị kẹt ở local optimum, không đảm bảo tìm được lời giải nếu trạng thái đầu xa đích hoặc có nhiều local optimum.
    2. Hoạt động
      Khởi tạo
        Đặt trạng thái hiện tại (current_state) là trạng thái đầu (initial_state).
      Lặp lại cho đến khi gặp goal hoặc không còn neighbor tốt hơn:
        Sinh tất cả các trạng thái láng giềng hợp lệ (neighbor).
        Tính heuristic cho từng neighbor.
        Nếu có neighbor nào tốt hơn (heuristic nhỏ hơn) trạng thái hiện tại:
        Chọn neighbor tốt nhất làm trạng thái hiện tại.
        Nếu không có neighbor nào tốt hơn, dừng lại.
      Kết thúc
        Nếu trạng thái hiện tại là goal, trả về đường đi.
        Nếu không, trả về "không tìm thấy lời giải" (bị kẹt ở local optimum).
    3. Giải thích các biến và cấu trúc
      current_state: Trạng thái hiện tại đang xét.
      neighbor: Các trạng thái láng giềng sinh ra từ current_state.
      heuristic: Hàm đánh giá độ gần với trạng thái đích (thường là tổng khoảng cách Manhattan).
      parent: Mỗi trạng thái lưu trạng thái cha, giúp truy vết đường đi nếu tìm thấy goal.
    4. Ưu nhược điểm của Simple Hill Climbing trong 8-puzzle
      Ưu điểm:
        Cài đặt rất đơn giản, dễ hiểu.
        Chạy rất nhanh với trạng thái đầu gần đích hoặc ít local optimum.
        Tiết kiệm bộ nhớ (chỉ cần lưu trạng thái hiện tại và neighbor).
      Nhược điểm:
        Dễ bị kẹt ở local optimum (trạng thái không phải goal nhưng không có neighbor nào tốt hơn).
        Không đảm bảo tìm được lời giải, đặc biệt với trạng thái đầu xa đích.
        Có thể phải chạy lại nhiều lần với trạng thái đầu khác nhau (random restart) để tăng khả năng thành công.

  ![Simple Hill Climbing Animation](https://github.com/DylanVenomania/AI_8Puzzle/raw/main/gifs/simple_cimb.gif)
  
  ### Stochastic Hill climbing
    1. Ý tưởng tổng quát của Stochastic Hill Climbing
      Stochastic Hill Climbing là một biến thể của thuật toán leo đồi (hill climbing).
      Thay vì luôn chọn neighbor tốt nhất (giống Simple/Steepest), thuật toán này chọn ngẫu nhiên một neighbor nào đó tốt hơn trạng thái hiện tại.
      Điều này giúp thuật toán có cơ hội thoát khỏi local optimum nhỏ (do không luôn chọn hướng tốt nhất mà chọn ngẫu nhiên trong các hướng tốt hơn).
      Ưu điểm: Giảm khả năng bị kẹt ở local optimum so với Simple Hill Climbing.
      Nhược điểm: Vẫn có thể bị kẹt nếu tất cả neighbor đều không tốt hơn, và không đảm bảo tìm được lời giải.
    2. Hoạt động
      Khởi tạo
        Đặt trạng thái hiện tại (current_state) là trạng thái đầu (initial_state).
      Lặp lại cho đến khi gặp goal hoặc không còn neighbor tốt hơn:
        Sinh tất cả các trạng thái láng giềng hợp lệ (neighbor).
        Chọn ra các neighbor có heuristic tốt hơn trạng thái hiện tại.
        Nếu có ít nhất một neighbor tốt hơn:
        Chọn ngẫu nhiên một trong số các neighbor tốt hơn này làm trạng thái hiện tại.
        Nếu không có neighbor nào tốt hơn, dừng lại.
      Kết thúc
        Nếu trạng thái hiện tại là goal, trả về đường đi.
        Nếu không, trả về "không tìm thấy lời giải" (bị kẹt ở local optimum).
    3. Giải thích các biến và cấu trúc
      current_state: Trạng thái hiện tại đang xét.
      neighbor: Các trạng thái láng giềng sinh ra từ current_state.
      better_neighbors: Danh sách neighbor có heuristic tốt hơn current_state.
      heuristic: Hàm đánh giá độ gần với trạng thái đích (thường là tổng khoảng cách Manhattan).
      parent: Mỗi trạng thái lưu trạng thái cha, giúp truy vết đường đi nếu tìm thấy goal.
    4. Ưu nhược điểm của Stochastic Hill Climbing trong 8-puzzle
      Ưu điểm:
        Đơn giản, dễ cài đặt.
        Có khả năng thoát khỏi một số local optimum nhỏ nhờ chọn ngẫu nhiên.
        Tiết kiệm bộ nhớ, chỉ cần lưu trạng thái hiện tại và neighbor.
      Nhược điểm:
        Vẫn có thể bị kẹt ở local optimum nếu không còn neighbor tốt hơn.
        Không đảm bảo tìm được lời giải, đặc biệt với trạng thái đầu xa đích hoặc có nhiều local optimum lớn.
        Đường đi có thể không tối ưu (không ngắn nhất).
        
  ![Stochastic Animation](https://github.com/DylanVenomania/AI_8Puzzle/raw/main/gifs/stochastic.gif)
  
  ### Steepest Ascent hill climbing
    1. Ý tưởng tổng quát của Steepest Ascent Hill Climbing
      Steepest Ascent Hill Climbing là một biến thể của thuật toán leo đồi (hill climbing).
      Ở mỗi bước, thuật toán xét tất cả các trạng thái láng giềng (neighbor) của trạng thái hiện tại và luôn chọn neighbor có giá trị heuristic tốt nhất (nhỏ nhất) để chuyển sang.
      Nếu không có neighbor nào tốt hơn trạng thái hiện tại, thuật toán dừng lại (bị kẹt ở local optimum).
      Ưu điểm: Luôn chọn hướng cải thiện tốt nhất, giúp tăng tốc độ hội tụ đến cực trị cục bộ.
      Nhược điểm: Dễ bị kẹt ở local optimum, không đảm bảo tìm được lời giải nếu trạng thái đầu xa đích hoặc có nhiều local optimum.
    2. Hoạt động
      Khởi tạo
        Đặt trạng thái hiện tại (current_state) là trạng thái đầu (initial_state).
      Lặp lại cho đến khi gặp goal hoặc không còn neighbor tốt hơn:
        Sinh tất cả các trạng thái láng giềng hợp lệ (neighbor).
        Tính heuristic cho từng neighbor.
        Tìm neighbor có heuristic tốt nhất (nhỏ nhất).
        Nếu heuristic của neighbor tốt nhất nhỏ hơn trạng thái hiện tại:
        Chuyển sang neighbor tốt nhất đó.
        Nếu không có neighbor nào tốt hơn, dừng lại.
      Kết thúc
        Nếu trạng thái hiện tại là goal, trả về đường đi.
        Nếu không, trả về "không tìm thấy lời giải" (bị kẹt ở local optimum).
    3. Giải thích các biến và cấu trúc
      current_state: Trạng thái hiện tại đang xét.
      neighbor: Các trạng thái láng giềng sinh ra từ current_state.
      best_neighbor: Neighbor có giá trị heuristic tốt nhất.
      heuristic: Hàm đánh giá độ gần với trạng thái đích (thường là tổng khoảng cách Manhattan).
      parent: Mỗi trạng thái lưu trạng thái cha, giúp truy vết đường đi nếu tìm thấy goal.
    4. Ưu nhược điểm của Steepest Ascent Hill Climbing trong 8-puzzle
      Ưu điểm:
        Luôn chọn hướng cải thiện tốt nhất, giúp tối ưu hóa nhanh hơn Simple Hill Climbing.
        Cài đặt đơn giản, dễ hiểu.
        Tiết kiệm bộ nhớ (chỉ cần lưu trạng thái hiện tại và neighbor).
      Nhược điểm:
        Rất dễ bị kẹt ở local optimum (trạng thái không phải goal nhưng không có neighbor nào tốt hơn).
        Không đảm bảo tìm được lời giải, đặc biệt với trạng thái đầu xa đích hoặc nhiều local optimum.
        Đường đi có thể không ngắn nhất

  ### Simulated Anealling
  
  ![Simulated Annealing Animation](https://github.com/DylanVenomania/AI_8Puzzle/raw/main/gifs/anealing.gif)
  
  ### Beam Search
    1. Ý tưởng tổng quát của Beam Search
      Beam Search là thuật toán tìm kiếm heuristic, kết hợp giữa BFS và Greedy.
      Ở mỗi bước, thuật toán chỉ giữ lại một số lượng giới hạn các trạng thái tốt nhất ( khoảng cách Manhattan đến đích) : là "beam width".
      Điều này giúp giảm bùng nổ trạng thái như BFS, nhưng vẫn ưu tiên các trạng thái gần đích hơn.
      Ưu điểm: Tốc độ nhanh, tiết kiệm bộ nhớ hơn BFS, dễ kiểm soát độ rộng tìm kiếm.
      Nhược điểm: Không đảm bảo tìm được lời giải, có thể bỏ lỡ lời giải nếu beam width quá nhỏ.
    2. Hoạt động
      Khởi tạo
        Đặt current_beam là danh sách chỉ chứa trạng thái đầu (initial_state).
      Lặp lại cho đến khi tìm thấy goal hoặc hết bước (max_steps):
        Với mỗi trạng thái trong current_beam, sinh tất cả neighbor (trạng thái con hợp lệ).
        Tính heuristic cho từng neighbor.
        Gom tất cả neighbor vào một danh sách next_beam.
        Sắp xếp next_beam theo heuristic tăng dần, chỉ giữ lại beam_width trạng thái tốt nhất cho bước tiếp theo.
        Nếu trong next_beam có trạng thái là goal, trả về đường đi.
      Gán current_beam = next_beam và lặp lại.
      Kết thúc
        Nếu sau số bước tối đa vẫn chưa tìm thấy goal, trả về "không tìm thấy lời giải".
    3. Giải thích các biến và cấu trúc
      beam_width: Số lượng trạng thái tối đa giữ lại ở mỗi bước (quyết định độ rộng tìm kiếm).
      current_beam: Danh sách các trạng thái hiện tại đang xét ở mỗi bước.
      next_beam: Danh sách các trạng thái sinh ra từ current_beam, sẽ được chọn lọc cho bước tiếp theo.
      heuristic: Hàm đánh giá độ gần với trạng thái đích (thường là tổng khoảng cách Manhattan).
      visited: Tập hợp các trạng thái đã duyệt, giúp tránh lặp lại.
      parent: Mỗi trạng thái lưu trạng thái cha, giúp truy vết đường đi nếu tìm thấy goal.
    4. Ưu nhược điểm của Beam Search trong 8-puzzle
      Ưu điểm:
        Tốc độ nhanh, tiết kiệm bộ nhớ hơn BFS vì chỉ giữ lại một số trạng thái tốt nhất.
        Dễ điều chỉnh độ rộng tìm kiếm bằng cách thay đổi beam_width.
        Có thể tìm ra lời giải nhanh nếu beam_width đủ lớn và heuristic tốt.
      Nhược điểm:
        Không đảm bảo tìm được lời giải (có thể bị bỏ lỡ lời giải nếu beam_width quá nhỏ).
        Đường đi có thể không ngắn nhất
        Phụ thuộc mạnh vào giá trị beam_width và chất lượng heuristic.
  ![Beam Search Animation](https://github.com/DylanVenomania/AI_8Puzzle/raw/main/gifs/beam_search.gif)
  
  ### And or 
    1. Ý tưởng tổng quát của Simulated Annealing
      Simulated Annealing là thuật toán tìm kiếm cục bộ (local search), được lấy ý tưởng từ quá trình ủ nhiệt luyện kim.
      Ở mỗi bước, thuật toán có thể chấp nhận chuyển sang trạng thái xấu hơn (heuristic cao hơn) với một xác suất nhất định, xác suất này giảm dần theo nhiệt độ.
      Khi nhiệt độ cao, thuật toán dễ chấp nhận trạng thái xấu hơn (giúp thoát local optimum); khi nhiệt độ thấp, thuật toán gần như giống hill climbing.
      Ưu điểm: Có thể thoát khỏi local optimum, tăng khả năng tìm được lời giải toàn cục.
      Nhược điểm: Không đảm bảo tìm được lời giải, phụ thuộc vào tham số nhiệt độ và tốc độ làm nguội.
    2. Hoạt động
      Khởi tạo
        Đặt trạng thái hiện tại (current_state) là trạng thái đầu (initial_state).
        Đặt nhiệt độ ban đầu (temp) và hệ số làm nguội (cooling_rate).
      Lặp lại cho đến khi gặp goal, nhiệt độ gần 0 hoặc hết bước:
        Sinh ngẫu nhiên một trạng thái láng giềng hợp lệ (neighbor).
        Tính heuristic của neighbor và current_state.
        Nếu neighbor tốt hơn (heuristic thấp hơn), chuyển sang neighbor.
        Nếu neighbor xấu hơn, chuyển sang neighbor với xác suất:
            P = exp(-(delta_energy) / temp)
            (delta_energy = heuristic(neighbor) - heuristic(current_state))
             Giảm nhiệt độ: temp = temp * cooling_rate.
      Kết thúc
      Nếu trạng thái hiện tại là goal, trả về đường đi.
      Nếu không, trả về "không tìm thấy lời giải" (bị kẹt hoặc hết nhiệt độ).
    3. Giải thích các biến và cấu trúc
      current_state: Trạng thái hiện tại đang xét.
      neighbor: Trạng thái láng giềng được chọn ngẫu nhiên.
      heuristic: Hàm đánh giá độ gần với trạng thái đích (thường là tổng khoảng cách Manhattan).
      temp (temperature): Nhiệt độ hiện tại, quyết định xác suất chấp nhận trạng thái xấu hơn.
      cooling_rate: Tốc độ làm nguội (thường nhỏ hơn 1, ví dụ 0.99).
      delta_energy: Hiệu heuristic giữa neighbor và current_state.
      parent: Mỗi trạng thái lưu trạng thái cha, giúp truy vết đường đi nếu tìm thấy goal.
    4. Ưu nhược điểm của Simulated Annealing trong 8-puzzle
    Ưu điểm:
      Có khả năng thoát khỏi local optimum nhờ cơ chế chấp nhận trạng thái xấu hơn.
      Phù hợp cho các bài toán có nhiều local optimum, trạng thái đầu xa đích.
      Tiết kiệm bộ nhớ, chỉ cần lưu trạng thái hiện tại và neighbor.
    Nhược điểm:
      Không đảm bảo tìm được lời giải (có thể hết nhiệt độ trước khi tới goal).
      Kết quả phụ thuộc mạnh vào tham số (nhiệt độ ban đầu, cooling_rate, số bước).
      Đường đi thường không ngắn nhất.

    
  ![And-Or Animation](https://github.com/DylanVenomania/AI_8Puzzle/raw/main/gifs/and-or.gif)
  ### Partially Observable ( Nhìn thấy 1 phần )
  ![Nhìn thay 1 phần Animation](https://github.com/DylanVenomania/AI_8Puzzle/raw/main/gifs/Nh%C3%ACn_thay_1_phan.gif)
  ### Back tracking 
  ![Backtrack Animation](https://github.com/DylanVenomania/AI_8Puzzle/raw/main/gifs/backtrack.gif)
    
