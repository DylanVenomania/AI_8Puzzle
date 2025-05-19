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
  ![A Star Animation](https://github.com/DylanVenomania/AI_8Puzzle/raw/main/gifs/A_star.gif)
  ### IDA*
  ![IDA Star Animation](https://github.com/DylanVenomania/AI_8Puzzle/raw/main/gifs/IDA_star.gif)
  ### Simple hill climbing 
  ### Stochastic Hill climbing
  ![Stochastic Animation](https://github.com/DylanVenomania/AI_8Puzzle/raw/main/gifs/stochastic.gif)
  ### Stepest Ascent hill climbing
  
  ### Beam Search
  ![Beam Search Animation](https://github.com/DylanVenomania/AI_8Puzzle/raw/main/gifs/beam_search.gif)
  ### And or 
  ![And-Or Animation](https://github.com/DylanVenomania/AI_8Puzzle/raw/main/gifs/and-or.gif)
  ### Partially Observable ( Nhìn thấy 1 phần )
  ![Nhìn thay 1 phần Animation](https://github.com/DylanVenomania/AI_8Puzzle/raw/main/gifs/Nh%C3%ACn_thay_1_phan.gif)
  ### Back tracking 
  ![Backtrack Animation](https://github.com/DylanVenomania/AI_8Puzzle/raw/main/gifs/backtrack.gif)
    
