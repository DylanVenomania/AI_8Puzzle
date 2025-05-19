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
  ### 2.4 Các thành phần khác
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

  #### UCS
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
  
  #### IDS
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
  
  #### Greedy
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
  #### A*
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
  #### IDA*
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
  
  #### Simple hill climbing 
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
  
  #### Stochastic Hill climbing
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
  
  #### Steepest Ascent hill climbing
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

  #### Simulated Anealling
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
  ![Simulated Annealing Animation](https://github.com/DylanVenomania/AI_8Puzzle/raw/main/gifs/anealing.gif)
  
  #### Beam Search
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
  
  #### And or 

    5. Lý do code AND-OR Search luôn trả về "không tìm thấy lời giải"
      Giới hạn số bước tối đa (max_steps):
      Trong quá trình cài đặt AND-OR Search cho 8-puzzle, để tránh việc thuật toán chạy quá lâu hoặc rơi vào vòng lặp vô hạn, em đã thêm một tham số giới hạn số bước tối đa ( max_depth). Khi số bước thực hiện vượt quá giới hạn này mà chưa tìm thấy lời giải, thuật toán sẽ dừng lại và trả về "không tìm thấy lời giải".
      Ảnh hưởng thực tế:
      Với 8-puzzle, không gian trạng thái rất lớn, cây AND-OR có thể phân nhánh cực kỳ mạnh (đặc biệt nếu môi trường động hoặc có nhiều mục tiêu con). Nếu max_steps đặt quá thấp (so với độ sâu cần thiết để giải), thuật toán sẽ không đủ thời gian hoặc số bước để duyệt tới trạng thái goal, dẫn đến việc mọi trạng thái đầu vào đều trả về "không tìm thấy lời giải".
      Vì sao lại cần max_steps:
        Tránh bùng nổ trạng thái và treo chương trình.
        Bảo vệ tài nguyên hệ thống khi chạy thử nghiệm.
        Nhưng nếu giá trị này không phù hợp với độ phức tạp thực tế, nó sẽ làm thuật toán không thể tìm ra lời giải dù trạng thái đầu vào hoàn toàn hợp lệ.
    ---> Tóm lại, lý do là em đã đặt max_steps còn thấp, chưa đủ tiêu chí cao để nó có thể giải được. ( max_depth hiện tại = 20 )
    Cô có thể thay đổi max_depth để nó có thể chạy được.
  ![And-Or Animation](https://github.com/DylanVenomania/AI_8Puzzle/raw/main/gifs/and-or.gif)

  #### Genetic ( Di truyền )
    1. Ý tưởng tổng quát của Genetic Algorithm
      Genetic Algorithm (GA) là thuật toán tối ưu hóa lấy cảm hứng từ tiến hóa sinh học tự nhiên.
      Mỗi cá thể (individual) đại diện cho một lời giải tiềm năng (thường là một hoán vị các số 0-8 cho 8-puzzle).
      Quần thể (population) gồm nhiều cá thể, qua các thế hệ sẽ được chọn lọc, lai ghép (crossover), đột biến (mutation) để tạo ra các cá thể tốt hơn.
      Ưu điểm: Có thể tìm ra lời giải trong không gian trạng thái rất lớn, dễ mở rộng cho các bài toán phức tạp.
      Nhược điểm: Không đảm bảo tìm được lời giải tối ưu, kết quả phụ thuộc vào tham số và cách mã hóa lời giải.
    2. Hoạt động
      Khởi tạo
        Sinh ngẫu nhiên một quần thể các cá thể (population), mỗi cá thể là một hoán vị hợp lệ của các số trong 8-puzzle.
      Lặp lại qua các thế hệ (generations):
        Đánh giá (Evaluation): Tính fitness (thường là tổng khoảng cách Manhattan đến goal) cho từng cá thể.
        Chọn lọc (Selection): Chọn ra các cá thể tốt nhất (elite) để giữ lại, hoặc chọn ngẫu nhiên theo xác suất tỉ lệ nghịch với fitness.
        Lai ghép (Crossover): Kết hợp hai cá thể cha mẹ để tạo ra cá thể con (thường dùng order crossover).
        Đột biến (Mutation): Thay đổi ngẫu nhiên một phần nhỏ của cá thể (ví dụ hoán đổi vị trí hai số) với xác suất mutation_rate.
        Thay thế: Tạo quần thể mới từ các cá thể con và elite.
      Kết thúc
        Nếu có cá thể đạt fitness = 0 (giống goal), hoặc hết số thế hệ, dừng lại.
        Dùng thuật toán tìm kiếm (ví dụ A*) để tìm đường đi thật sự từ initial_state đến cá thể tốt nhất (nếu cần).
    3. Giải thích các biến và cấu trúc
      population: Danh sách các cá thể (mỗi cá thể là một hoán vị các số 0-8).
      individual: Một lời giải tiềm năng (một cá thể).
      fitness: Giá trị đánh giá chất lượng cá thể (thường là tổng khoảng cách Manhattan đến goal).
      elite_size: Số cá thể tốt nhất giữ lại qua mỗi thế hệ.
      mutation_rate: Xác suất đột biến một cá thể.
      generations: Số thế hệ tiến hóa.
      crossover: Hàm lai ghép hai cá thể cha mẹ để tạo ra cá thể con.
      mutation: Hàm đột biến cá thể.
      goal_state: Trạng thái đích cần đạt được.
    4. Ưu nhược điểm của Genetic Algorithm trong 8-puzzle
      Ưu điểm:
        Có thể tìm ra lời giải trong không gian trạng thái lớn, kể cả khi các thuật toán truyền thống gặp khó.
        Dễ mở rộng cho các bài toán phức tạp hơn 8-puzzle.
        Có thể vượt qua local optimum nhờ đột biến và lai ghép.
      Nhược điểm:
        Không đảm bảo tìm được lời giải tối ưu (có thể chỉ tìm được lời giải gần đúng).
        Kết quả phụ thuộc mạnh vào tham số (population size, mutation rate, generations, ...).
        Tốc độ chậm hơn các thuật toán heuristic nếu chỉ giải các bài toán nhỏ.
        Đường đi tìm được thường không ngắn nhất, cần kết hợp với thuật toán khác (A*) để tối ưu hóa đường đi cuối cùng.

![Genetic Algorithm Animation](https://github.com/DylanVenomania/AI_8Puzzle/raw/main/gifs/genetic.gif)
        
  #### Partially Observable ( Nhìn thấy 1 phần )
    1. Ý tưởng tổng quát của AND-OR Search
      AND-OR Search là thuật toán tìm kiếm được sử dụng cho các bài toán có cấu trúc phân rã mục tiêu thành các mục tiêu con (AND) hoặc có nhiều lựa chọn (OR).
      Trong cây tìm kiếm:
        nút OR: Có nhiều lựa chọn, chỉ cần một nhánh thành công là đủ (giống như các thuật toán tìm kiếm truyền thống).
        nút AND : Phải giải quyết thành công tất cả các nhánh con mới thành công (dùng cho các bài toán phân rã thành nhiều mục tiêu con đồng thời).
      Trong 8-puzzle, AND-OR Search thường dùng để mô phỏng các bài toán phức tạp hơn, hoặc các tình huống có nhiều mục tiêu con, hoặc môi trường không xác định.
      Ưu điểm: Có thể giải quyết các bài toán với cấu trúc mục tiêu phức tạp, nhiều điều kiện rẽ nhánh.
      Nhược điểm: Bùng nổ trạng thái rất lớn, tốn bộ nhớ, ít dùng cho 8-puzzle chuẩn.
    2. Hoạt động
      Khởi tạo
        Bắt đầu từ trạng thái đầu (initial_state).
      Đệ quy mở rộng trạng thái:
        Nếu trạng thái hiện tại là goal, trả về thành công.
        Nếu là nút OR:
          Thử từng hành động hợp lệ, nếu có ít nhất một nhánh con thành công thì node này thành công.
        Nếu là nút AND:
          Phải giải quyết thành công tất cả các nhánh con (tức là tất cả hành động hoặc mục tiêu con đều phải thành công).
          Dùng memoization để lưu lại kết quả các trạng thái đã duyệt, tránh lặp lại.
      Kết thúc
        Nếu giải được toàn bộ cây AND-OR, trả về lời giải; nếu không, trả về thất bại.
    3. Giải thích các biến và cấu trúc
      AND node: Node yêu cầu giải quyết đồng thời nhiều mục tiêu con.
      OR node: Node chỉ cần một nhánh con thành công.
      memo: Bộ nhớ lưu kết quả các trạng thái đã duyệt để tránh lặp lại (memoization).
      current_state: Trạng thái hiện tại đang xét.
      goal_state: Trạng thái đích cần tìm.
      max_depth, max_memory: Giới hạn độ sâu và bộ nhớ để tránh bùng nổ trạng thái.
      parent: Lưu trạng thái cha để truy vết đường đi nếu cần.
    4. Ưu nhược điểm của AND-OR Search trong 8-puzzle
      Ưu điểm:    
        Có thể giải quyết các bài toán với nhiều mục tiêu con hoặc điều kiện rẽ nhánh phức tạp.
        Phù hợp với các bài toán trong môi trường không xác định, đa tác nhân, hoặc cần phân rã mục tiêu.
      Nhược điểm:
        Bùng nổ trạng thái rất lớn, tốn nhiều bộ nhớ và thời gian --> Khiến luôn không giải được trong thuật toán 8 Puzzle do quá là nhiều trạng thái
        Không thực tế cho 8-puzzle chuẩn (vì thường chỉ có một mục tiêu duy nhất).
        Cài đặt phức tạp hơn các thuật toán tìm kiếm truyền thống.
  ![Nhìn thay 1 phần Animation](https://github.com/DylanVenomania/AI_8Puzzle/raw/main/gifs/Nh%C3%ACn_thay_1_phan.gif)

  #### Dynamic Environment ( Môi trường động ) - thay thế cho Belief State 
    1. Ý tưởng tổng quát của Dynamic Environment
      Dynamic Environment là mô hình mô phỏng môi trường động, nơi trạng thái của bài toán (hoặc các yếu tố liên quan) có thể thay đổi trong quá trình giải quyết, không còn cố định như môi trường truyền thống.
      Trong 8-puzzle, điều này có thể bao gồm: trạng thái goal thay đổi, các quy tắc di chuyển thay đổi, hoặc trạng thái hiện tại bị tác động bởi các yếu tố bên ngoài (ví dụ: người chơi khác, tác nhân ngẫu nhiên).
      Ưu điểm: Cho phép kiểm thử thuật toán trong các tình huống thực tế hơn, nơi môi trường không tĩnh và có thể thay đổi bất ngờ.
      Nhược điểm: Làm cho việc giải quyết bài toán phức tạp hơn, khó đảm bảo tính tối ưu hoặc đúng tuyệt đối như môi trường tĩnh.
    2. Hoạt động
    Khởi tạo
      Tạo một đối tượng môi trường động (DynamicPuzzleEnvironment) với trạng thái đầu (initial_state).
      Xác định các quy tắc động: có thể là goal_state thay đổi, trạng thái bị tác động ngoài ý muốn, hoặc chỉ quan sát được một phần trạng thái (partially observable).
    Trong quá trình giải
      Khi thực hiện một bước di chuyển (apply_move), môi trường có thể:
      Cập nhật trạng thái thực tế dựa trên hành động.
      Thay đổi trạng thái goal hoặc các ràng buộc khác.
      Cập nhật các trạng thái tin tưởng (belief states) nếu chỉ quan sát được một phần trạng thái.
      Ở mỗi bước, thuật toán phải kiểm tra lại trạng thái hiện tại, goal, và các điều kiện môi trường.
    Kết thúc
      Khi đạt được trạng thái goal (dù goal có thể đã thay đổi), trả về lời giải.
      Nếu môi trường thay đổi khiến goal không còn đạt được, báo thất bại.
    3. Giải thích các biến và cấu trúc
      DynamicPuzzleEnvironment: Lớp mô phỏng môi trường động, quản lý trạng thái hiện tại, goal, và các quy tắc động.
      current_state: Trạng thái hiện tại của puzzle (có thể bị thay đổi ngoài ý muốn).
      goal_state: Trạng thái đích, có thể thay đổi trong quá trình giải.
      apply_move(move): Hàm thực hiện di chuyển, đồng thời cập nhật lại trạng thái dựa vào các quy tắc động.
      belief_states: (Nếu có) Tập hợp các trạng thái tin tưởng về tình hình thực tế, dùng khi môi trường chỉ quan sát được một phần.
      _update_observed_positions(): Hàm cập nhật các ô đã quan sát được trên bảng.
    4. Ưu nhược điểm của Dynamic Environment trong 8-puzzle
    Ưu điểm:
      Mô phỏng được các tình huống thực tế, nơi môi trường không cố định.
      Cho phép kiểm thử thuật toán trong điều kiện khó, kiểm tra khả năng thích nghi.
      Hỗ trợ các bài toán nâng cao như multi-agent, môi trường không xác định, hoặc bài toán chỉ quan sát một phần.
    Nhược điểm:    
      Làm tăng độ phức tạp của thuật toán và kiểm thử.
      Khó đảm bảo tính tối ưu, vì môi trường có thể thay đổi bất ngờ.
      Việc truy vết đường đi và xác định trạng thái goal có thể không còn chính xác tuyệt đối.
    5. Lý do thay thế cho belief state và giải thích lựa chọn
    Belief State truyền thống là gì?
      Belief State là khái niệm trong AI dùng để mô tả tập hợp các trạng thái mà tác nhân cho là có thể xảy ra, khi môi trường không hoàn toàn quan sát được (partially observable).
      Trong 8-puzzle, belief state thường dùng khi không biết chính xác trạng thái hiện tại 
    Vì sao thay bằng Dynamic Environment?
      Đơn giản hóa cài đặt: Dynamic Environment cho phép quản lý trạng thái, goal, và các quy tắc động một cách trực tiếp, dễ kiểm soát và dễ tích hợp với các thuật toán tìm kiếm truyền thống.
      Linh hoạt hơn: Dynamic Environment có thể kết hợp cả các yếu tố của belief state, nhưng cũng có thể mô phỏng các tình huống mà belief state không mô tả được (ví dụ: goal thay đổi liên tục, trạng thái bị reset bất ngờ).
      Tối ưu hiệu năng: Việc duy trì một tập hợp lớn các belief states rất tốn bộ nhớ và phức tạp, trong khi môi trường động có thể đơn giản chỉ cần cập nhật trạng thái hiện tại và goal.
    6. Kết luận
      Dynamic Environment là giải pháp thực tiễn, linh hoạt và dễ mở rộng hơn cho project 8-puzzle, nhất là khi muốn kiểm thử các thuật toán trong môi trường thay đổi, hoặc chỉ cần một mức độ "tin tưởng" đơn giản thay vì mô hình hóa toàn bộ belief state.
      Nếu sau này cần mở rộng để mô phỏng môi trường chỉ quan sát được một phần (partially observable), bạn vẫn có thể tích hợp belief state vào Dynamic Environment một cách tự nhiên.
  ![Dynamic Programming Animation](https://github.com/DylanVenomania/AI_8Puzzle/raw/main/gifs/dynamic.gif)
  
  #### Back tracking 
    1. Ý tưởng tổng quát của Backtracking
      Backtracking là thuật toán thử - sai, xây dựng lời giải từng bước một, nếu phát hiện không thể tiếp tục thì quay lui về bước trước để thử hướng khác.
      Trong 8-puzzle (dưới dạng CSP), mỗi vị trí trên bảng là một biến, giá trị là các số từ 0-8, ràng buộc là không trùng số và đúng cấu hình đích.
      Ưu điểm: Đảm bảo tìm được lời giải nếu tồn tại, cài đặt đơn giản, dễ hiểu.
      Nhược điểm: Tốc độ chậm, dễ bị bùng nổ trạng thái nếu không tối ưu hóa kiểm tra ràng buộc.
    2. Hoạt động
      Khởi tạo
        Xác định tập biến (các vị trí trên bảng), domain (các giá trị có thể gán cho từng biến), assignment (gán giá trị cho biến).
      Lặp lại cho đến khi gán hết biến hoặc không còn giá trị hợp lệ:
        Chọn một biến chưa gán giá trị.
        Thử từng giá trị trong domain của biến đó:
        Gán giá trị cho biến.
        Kiểm tra ràng buộc (không trùng số, hợp lệ với goal_state...).
        Nếu hợp lệ, tiếp tục gán biến tiếp theo (đệ quy).
        Nếu không hợp lệ hoặc không tìm được lời giải ở nhánh này, quay lui (bỏ gán giá trị vừa thử và thử giá trị khác).
        Nếu đã gán hết biến và assignment thỏa mãn tất cả ràng buộc, trả về lời giải.
    Kết thúc
      Nếu thử hết các khả năng mà không tìm được assignment hợp lệ, trả về "không tìm thấy lời giải".
    3. Giải thích các biến và cấu trúc
      variables: Danh sách các biến (vị trí trên bảng 8-puzzle).
      domains: Domain các giá trị có thể gán cho từng biến (thường là 0-8, loại bỏ các giá trị đã gán ở các biến trước).
      assignment: Dictionary lưu giá trị đã gán cho từng biến.
      goal_state: Trạng thái đích cần đạt được.
      check_constraints: Hàm kiểm tra ràng buộc (không trùng số, đúng cấu hình...).
      backtrack: Hàm đệ quy thực hiện quá trình thử - sai và quay lui.
    4. Ưu nhược điểm của Backtracking trong 8-puzzle
    Ưu điểm:
      Đảm bảo tìm được lời giải nếu tồn tại (nếu không bị giới hạn thời gian/bộ nhớ).
      Cài đặt đơn giản, dễ hiểu.
      Linh hoạt, có thể kết hợp với các kỹ thuật tối ưu hóa khác (Forward Checking, AC-3...).
    Nhược điểm:    
      Rất chậm với không gian trạng thái lớn, dễ bị bùng nổ trạng thái.
      Không phù hợp để giải 8-puzzle với trạng thái đầu xa đích hoặc cấu hình phức tạp.
      Hiệu quả phụ thuộc mạnh vào thứ tự gán biến và kiểm tra ràng buộc.
  ![Backtrack Animation](https://github.com/DylanVenomania/AI_8Puzzle/raw/main/gifs/backtrack.gif)

  #### Forward Checking
    1. Ý tưởng tổng quát của Forward Checking
      Forward Checking là một kỹ thuật tối ưu hóa trong giải bài toán ràng buộc (CSP).
      Khi gán giá trị cho một biến, Forward Checking sẽ loại bỏ giá trị đó khỏi domain của các biến chưa gán, kiểm tra trước xem có biến nào sẽ bị  domain rỗng không.
      Nếu phát hiện có biến nào không còn giá trị hợp lệ để gán, thuật toán sẽ quay lui ngay lập tức , tránh mở rộng các nhánh không thể thành công.
      Ưu điểm: Giảm mạnh số lượng trạng thái cần duyệt so với backtracking thuần túy, tăng tốc độ giải bài toán CSP.
      Nhược điểm: Tốn thêm bộ nhớ và thời gian để cập nhật và kiểm tra domain của các biến.
    2. Hoạt động
    Khởi tạo
      Xác định tập biến (vị trí trên bảng), domain (các giá trị có thể gán cho từng biến), assignment (gán giá trị cho biến).
    Lặp lại cho đến khi gán hết biến hoặc không còn giá trị hợp lệ:
      Chọn một biến chưa gán giá trị.
      Thử từng giá trị trong domain của biến đó:
      Gán giá trị cho biến.
      Forward Checking: Loại giá trị vừa gán khỏi domain của các biến chưa gán.
      Kiểm tra domain của các biến chưa gán:
        Nếu có biến nào domain rỗng, quay lui ngay (không mở rộng nhánh này nữa).
        Nếu tất cả domain còn giá trị, tiếp tục gán biến tiếp theo (đệ quy).
        Khi quay lui, khôi phục lại domain ban đầu (undo).
        Nếu đã gán hết biến và assignment thỏa mãn tất cả ràng buộc, trả về lời giải.
    Kết thúc
      Nếu thử hết các khả năng mà không tìm được assignment hợp lệ, trả về "không tìm thấy lời giải".
    3. Giải thích các biến và cấu trúc
      variables: Danh sách các biến (vị trí trên bảng 8-puzzle).
      domains: Domain các giá trị có thể gán cho từng biến (thường là 0-8, loại bỏ các giá trị đã gán ở các biến trước).
      assignment: Dictionary lưu giá trị đã gán cho từng biến.
      goal_state: Trạng thái đích cần đạt được.
      forward_check: Hàm thực hiện loại giá trị khỏi domain các biến chưa gán khi gán giá trị cho một biến.
      backtrack: Hàm đệ quy thực hiện quá trình thử - sai và quay lui.
    4. Ưu nhược điểm của Forward Checking trong 8-puzzle
      Ưu điểm:
        Giảm số lượng nhánh phải duyệt, tăng tốc độ giải bài toán CSP so với backtracking thuần túy.
        Phát hiện sớm các nhánh không thể thành công, tiết kiệm thời gian.
        Dễ kết hợp với các kỹ thuật khác như MRV (Minimum Remaining Values).
      Nhược điểm:
        Tốn thêm bộ nhớ để lưu domain của các biến.
        Cần cài đặt logic undo (khôi phục domain khi quay lui).
        Không giải quyết được tất cả các trường hợp bế tắc phức tạp như AC-3.
  ![Forward Checking Animation](https://github.com/DylanVenomania/AI_8Puzzle/raw/main/gifs/forward.gif)
  
  #### AC3
    1. Ý tưởng tổng quát của AC-3
      AC-3 là một thuật toán kiểm tra tính nhất quán cung (arc consistency) trong các bài toán ràng buộc (CSP).
      AC-3 đảm bảo rằng với mỗi cặp biến liên quan bởi một ràng buộc, mọi giá trị trong domain của một biến đều có ít nhất một giá trị tương ứng hợp lệ trong domain của biến còn lại.
      Khi phát hiện giá trị nào không còn hợp lệ, thuật toán loại bỏ giá trị đó khỏi domain, và nếu domain của một biến thay đổi thì các cung liên quan đến biến đó sẽ được kiểm tra lại.
      Ưu điểm: Giảm mạnh không gian tìm kiếm, phát hiện sớm các trường hợp không có lời giải.
      Nhược điểm: Chỉ kiểm tra nhất quán cục bộ, không giải quyết được tất cả các trường hợp bế tắc phức tạp như global consistency.
    2. Hoạt động
    Khởi tạo
      Xác định tập biến (vị trí trên bảng), domain (các giá trị có thể gán cho từng biến), và tập các cung (arc) giữa các biến có ràng buộc trực tiếp (ví dụ: không trùng giá trị).
      Lặp lại cho đến khi hàng đợi cung rỗng:
      
      Lấy một cung (Xi, Xj) ra khỏi hàng đợi.
      Kiểm tra nhất quán cung: Với mỗi giá trị x trong domain của Xi, nếu không tồn tại giá trị y trong domain của Xj sao cho (x, y) thỏa mãn ràng buộc, thì loại x khỏi domain của Xi.
      Nếu domain của Xi bị thay đổi:
      Nếu domain rỗng, trả về "không có lời giải".
      Thêm tất cả các cung liên quan đến Xi (trừ Xj) vào hàng đợi để kiểm tra lại.
    Kết thúc
      Nếu tất cả domain đều còn giá trị, trả về domain đã rút gọn (có thể giải tiếp bằng backtracking/forward checking).
      Nếu có domain rỗng, thông báo là không có lời giải
    3. Giải thích các biến và cấu trúc
      variables: Danh sách các biến (vị trí trên bảng 8-puzzle).
      domains: Domain các giá trị có thể gán cho từng biến (thường là 0-8, loại bỏ các giá trị đã gán ở các biến trước).
      arcs: Tập các cung (Xi, Xj) giữa các biến có ràng buộc trực tiếp (thường là "không trùng giá trị").
      queue: Hàng đợi các cung cần kiểm tra nhất quán.
      revise(Xi, Xj): Hàm kiểm tra và loại bỏ các giá trị không còn hợp lệ khỏi domain của Xi dựa trên domain của Xj.
    4. Ưu nhược điểm của AC-3 trong 8-puzzle
      Ưu điểm:
        Loại bỏ sớm các giá trị không hợp lệ, giảm mạnh không gian tìm kiếm cho backtracking/forward checking.
        Phát hiện nhanh các trường hợp không có lời giải (domain rỗng).
        Có thể kết hợp với các kỹ thuật CSP khác để tăng hiệu quả.
      Nhược điểm:
        Chỉ đảm bảo nhất quán cục bộ (local consistency), không đảm bảo tìm được lời giải nếu bài toán phức tạp.
        Có thể tốn thời gian với các bài toán có nhiều biến và ràng buộc phức tạp.
        Không tự động sinh ra lời giải, chỉ rút gọn domain; cần kết hợp với backtracking để giải hoàn chỉnh.
  ![AC-3 Animation](https://github.com/DylanVenomania/AI_8Puzzle/raw/main/gifs/Ac-3.gif)
  
  #### Q Learning
    1. Ý tưởng tổng quát của Q-Learning
      Q-Learning là một thuật toán học tăng cường (reinforcement learning) không cần mô hình môi trường.
      Thuật toán học một hàm Q(state, action) đánh giá giá trị kỳ vọng của việc thực hiện action tại state, sau đó đi theo chính sách ( policy ) tối ưu dựa trên Q.
      Q-Learning giúp tác nhân học cách giải quyết bài toán thông qua thử nghiệm và rút kinh nghiệm từ phần thưởng (reward) nhận được.
      Ưu điểm: Có thể áp dụng cho các môi trường phức tạp, không cần biết trước mô hình chuyển trạng thái.
      Nhược điểm: Cần rất nhiều lần thử nghiệm (episode) để hội tụ, không thực tế cho 8-puzzle lớn do không gian trạng thái quá lớn.
    2. Hoạt động
    Khởi tạo
      Khởi tạo bảng Q, Q[state][action] = 0 cho tất cả các state, action có thể.
      Đặt các tham số: learning rate (alpha), discount factor (gamma), epsilon (xác suất chọn hành động ngẫu nhiên - exploration).
    Lặp lại qua nhiều episode:
      Đặt trạng thái hiện tại là initial_state.
      Lặp lại cho đến khi đến goal_state hoặc hết số bước tối đa:
      Chọn action: với xác suất epsilon, chọn ngẫu nhiên (exploration); còn lại chọn action có Q lớn nhất (exploitation).
      Thực hiện action, nhận về next_state và reward (ví dụ: +1 nếu đến goal, -0.1 mỗi bước).
      Cập nhật Q theo công thức:
              Q[state][action] += alpha * (reward + gamma * max(Q[next_state]) - Q[state][action])
      Chuyển sang next_state.
      Sau khi học xong, để tìm đường đi tối ưu, luôn chọn action có Q lớn nhất tại mỗi state.
    Kết thúc
      Khi Q hội tụ, policy tốt nhất là luôn chọn action có Q lớn nhất tại mỗi state.
    3. Giải thích các biến và cấu trúc
      Q: Bảng Q lưu giá trị cho mỗi cặp (state, action).
      state: Trạng thái hiện tại của puzzle (thường biểu diễn bằng tuple).
      action: Hành động hợp lệ tại state (di chuyển ô trống lên/xuống/trái/phải).
      alpha (learning rate): Tốc độ cập nhật Q.
      gamma (discount factor): Độ ưu tiên phần thưởng tương lai.
      epsilon: Xác suất chọn hành động ngẫu nhiên (exploration).
      reward: Phần thưởng nhận được sau mỗi action (có thể là -0.1 mỗi bước, +1 khi tới goal).
      episodes: Số lần lặp lại quá trình học.
      max_steps: Số bước tối đa mỗi episode.
    4. Ưu nhược điểm của Q-Learning trong 8-puzzle
    Ưu điểm:
      Không cần biết trước mô hình chuyển trạng thái của môi trường.
      Có thể áp dụng cho các bài toán phức tạp, môi trường động, không xác định.
      Policy học được có thể dùng lại cho các trạng thái tương tự.
    Nhược điểm:
      Cần rất nhiều episode để hội tụ, không thực tế cho 8-puzzle lớn do không gian trạng thái quá lớn.
      Tốn nhiều bộ nhớ để lưu bảng Q nếu số trạng thái/action lớn.
      Đường đi tìm được có thể không tối ưu nếu chưa hội tụ đủ.
  ![Q-Learning Animation](https://github.com/DylanVenomania/AI_8Puzzle/raw/main/gifs/q-learning.gif)


### 3.2 So sánh hiệu suất các thuật toán giải 8-puzzle
  #### 1. Tiêu chí đánh giá hiệu suất
    Tốc độ giải (thời gian chạy): Mất bao lâu để tìm ra lời giải.
    Bộ nhớ sử dụng: Dung lượng bộ nhớ cần thiết cho quá trình giải.
    Chất lượng lời giải: Độ ngắn của đường đi, có tối ưu không.
    Khả năng tìm ra lời giải: Có đảm bảo luôn tìm ra lời giải không.
  #### 2. So sánh chi tiết
    BFS (Breadth-First Search):
      Tốc độ giải trung bình, nhưng sử dụng rất nhiều bộ nhớ do phải lưu toàn bộ cây trạng thái. 
      Đảm bảo tìm ra lời giải tối ưu. 
      Phù hợp cho bài toán nhỏ.
    DFS (Depth-First Search):
      Có thể nhanh nếu gặp may, sử dụng ít bộ nhớ. 
      Tuy nhiên không đảm bảo tìm ra lời giải, dễ rơi vào vòng lặp vô hạn và không tìm được lời giải tối ưu.
    UCS (Uniform Cost Search):
      Tương tự BFS nhưng có trọng số, đảm bảo lời giải tối ưu. 
      Chạy chậm và tốn bộ nhớ lớn.
    IDS (Iterative Deepening Search):
      Kết hợp ưu điểm của BFS và DFS: sử dụng ít bộ nhớ, đảm bảo tìm được lời giải tối ưu. 
      Tuy nhiên, do phải lặp lại nhiều lần nên tốc độ chậm.
    Greedy Best-First Search:
      Chạy nhanh, sử dụng bộ nhớ vừa phải. 
      Không đảm bảo lời giải tối ưu, dễ bị kẹt ở local optimum nếu heuristic không tốt.
    A (A-star):
      Là thuật toán hiệu quả nhất nếu sử dụng heuristic tốt (khoảng cách Manhattan). 
      Đảm bảo tìm ra lời giải tối ưu, nhưng sử dụng nhiều bộ nhớ.
    IDA (Iterative Deepening A-star):
      Là phiên bản tiết kiệm bộ nhớ của A. 
      Đảm bảo tìm được lời giải tối ưu, nhưng có thể chậm hơn A.
    Hill Climbing:
      Chạy nhanh và dùng rất ít bộ nhớ, nhưng dễ bị kẹt ở local optimum. 
      Không đảm bảo tìm ra lời giải hoặc tối ưu.
    Stochastic / Steepest Ascent Hill Climbing:
      Cải tiến từ Hill Climbing giúp giảm khả năng kẹt ở local optimum. 
      Tuy nhiên vẫn không đảm bảo lời giải và không tối ưu.
    Beam Search:
      Chạy nhanh, hiệu quả nếu chọn beam width phù hợp. 
      Tuy nhiên dễ bỏ qua lời giải do không duy trì đầy đủ các nhánh.
    Simulated Annealing:
      Có thể thoát local optimum nhờ tính ngẫu nhiên. 
      Không đảm bảo tìm ra lời giải, hiệu suất phụ thuộc nhiều vào cấu hình nhiệt độ.
    Genetic Algorithm ( giải thuật di truyền )
      Tốc độ chậm, dùng nhiều bộ nhớ. 
      Phụ thuộc vào các tham số như tỷ lệ đột biến, chọn lọc... 
      Thường chỉ tìm được lời giải gần đúng, không tối ưu.
    Backtracking:
      Rất chậm nhưng sử dụng ít bộ nhớ. Có thể tìm được lời giải và đảm bảo tối ưu. 
      Tuy nhiên, không thực tế cho 8-puzzle vì không gian trạng thái quá lớn.
    Forward Checking:
      Là cải tiến của backtracking giúp loại bỏ giá trị không hợp lệ sớm hơn. 
      Tối ưu hơn về tốc độ nhưng vẫn không phù hợp với 8-puzzle lớn.
    AC-3 (Arc Consistency):
      Dùng để giảm miền giá trị trước khi tìm lời giải. 
      Không tự giải bài toán mà thường kết hợp với backtracking. 
      Không dùng riêng cho 8-puzzle.
    Q-Learning (Reinforcement Learning):
      Quá chậm, sử dụng rất nhiều bộ nhớ. 
      Không đảm bảo tìm ra lời giải nếu chưa hội tụ. 
      Không phù hợp cho 8-puzzle do không gian trạng thái rất lớn.

#### 3. Nhận xét tổng quát
     A* là lựa chọn tối ưu nếu sử dụng heuristic tốt (khoảng cách Manhattan).
     IDA phù hợp khi bộ nhớ giới hạn, tuy nhiên tốc độ có thể chậm hơn A*.
     Các thuật toán local search  không đảm bảo lời giải tối ưu, chủ yếu dùng để kiểm thử ý tưởng
     Các kỹ thuật CSP (Backtracking, FC, AC-3) không phù hợp với 8-puzzle ở quy mô thực tế.
     Q-Learning và các phương pháp học tăng cường thiếu hiệu quả với không gian trạng thái lớn như 8-puzzle.









    
