**Unit 1**

1. **Disjunctive Normal Form (DNF) for ~ (A → (~ B C))**
    
    - The given formula is ~ (A → (~ B ∧ C)).
    - An implication can be rewritten using logical equivalence: A → (~ B ∧ C) is the same as ~~A ∨ (~~ B ∧ C).
    - Applying De Morgan’s Theorem to the negation: ~(~~A ∨ (~~ B ∧ C)) is equivalent to A ∧ ~~(~~ B ∧ C).
    - Distributing the negation inside: A ∧ (B ∨ ~C).
    - Expanding the conjunction over the disjunction: (A ∧ B) ∨ (A ∧ ~C).
    - This is the required Disjunctive Normal Form.
2. **Machine Learning vs. Traditional Programming**
    
    - **Traditional Programming:** The programmer explicitly codes rules and logic to solve a specific problem. It follows a clear set of instructions defined by humans. If the problem changes, new rules must be manually added.
    - **Machine Learning:** Instead of defining rules manually, the system learns patterns from data. It generalizes from past examples to make predictions about new, unseen data.
    - **Example:** A spam filter in traditional programming relies on predefined keywords (e.g., "free money," "win now"). In contrast, a machine learning-based spam filter learns patterns from past emails labeled as spam or non-spam, enabling it to detect new spam emails more effectively.
3. **Dimensionality Reduction**
    
    - Many datasets have a large number of features, some of which may be redundant or irrelevant. Dimensionality reduction techniques help reduce the number of input variables while preserving essential information.
    - **Principal Component Analysis (PCA):** Identifies the most important directions in the dataset and projects data into a lower-dimensional space.
    - **t-SNE (t-Distributed Stochastic Neighbor Embedding):** A technique primarily used for visualizing high-dimensional data in two or three dimensions.
    - **Example:** In facial recognition, an image has thousands of pixels, but only a few features (e.g., eyes, nose, mouth) are necessary for identification. PCA can reduce the dimensionality while keeping essential details.
4. **First-Order Logic (FOL)**
    
    - FOL introduces quantifiers and predicates to represent statements about objects and their relationships.
    - **Syntax:** Uses variables (x, y), predicates (e.g., Human(x)), functions, logical connectives (¬, ∧, ∨, →), and quantifiers (∀, ∃).
    - **Semantics:** Assigns meaning to symbols.
    - **Example:** "Every student in the class passed the exam" can be expressed as: ∀x (Student(x) → PassedExam(x))
5. **AI Agents**
    
    - An AI agent perceives its environment through sensors and takes actions using actuators.
    - **Properties of Agents:** Autonomy, adaptability, goal-oriented behavior, and learning capability.
    - **Types:**
        - **Simple Reflex Agents:** Reacts based only on current perception (e.g., thermostat turning on heating when temperature drops).
        - **Model-Based Reflex Agents:** Maintains an internal model to track the environment (e.g., self-driving cars using previous locations).
6. **Intelligent Agents**
    
    - **Definition:** AI-driven entities capable of making decisions and adapting to their surroundings.
    - **Example:** Virtual assistants like Siri, Alexa, and Google Assistant, which understand commands and provide intelligent responses.
7. **Applications of AI**
    
    - **Healthcare:** Disease diagnosis, robotic surgeries, drug discovery.
    - **Finance:** Fraud detection, algorithmic trading, risk assessment.
    - **Retail:** Recommendation systems, customer support chatbots.
    - **Transportation:** Autonomous vehicles, traffic prediction, logistics optimization.
8. **Agent Environments**
    
    - **Fully Observable vs. Partially Observable:**
        - Fully Observable: Chess (all information is visible to the player).
        - Partially Observable: Poker (some cards are hidden from opponents).
    - **Deterministic vs. Stochastic:**
        - Deterministic: Chess (outcomes are predictable).
        - Stochastic: Weather prediction (uncertain outcomes due to external factors).
9. **Syntax and Semantics in Propositional Logic**
    
    - **Syntax:** Defines valid logical statements (e.g., (A ∨ B)).
    - **Semantics:** Assigns meaning to logical statements (e.g., "A" represents "It is raining").
10. **Unification of Predicates**
    
    - The process of making different logical expressions identical by finding appropriate substitutions.
    - **Example:** Loves(x, y) and Loves(John, Mary) unify with {x = John, y = Mary}.

---

**Unit 2**

1. **Depth-First Search (DFS) Algorithm**
    
    - **Algorithm:**
        1. Start at the root node.
        2. Explore as deep as possible before backtracking.
        3. Continue until the goal is found or all nodes are visited.
    - **Advantage:** Uses less memory compared to Breadth-First Search (BFS).
    - **Disadvantage:** Can get stuck in an infinite loop if not properly managed.
2. **Supervised vs. Unsupervised Learning**
    
    - **Supervised Learning:**
        - Training data is labeled.
        - Examples: Regression, classification (e.g., spam detection, image recognition).
    - **Unsupervised Learning:**
        - No labeled data; finds hidden patterns.
        - Examples: Clustering, dimensionality reduction (e.g., customer segmentation).
3. __A_ Search Algorithm_*
    
    - Finds the optimal path by minimizing total estimated cost (f(n) = g(n) + h(n)).
    - **Example:** GPS navigation where g(n) is distance traveled and h(n) estimates remaining distance.
4. **Search Algorithms**
    
    - **Recursive Best-First Search (RBFS):** Memory-efficient version of A*.
    - **Heuristic Functions:** Guides search algorithms to reduce computation time.
    - **Minimax Algorithm:** Used in two-player games to determine the best move.
    - **Alpha-Beta Pruning:** Optimizes Minimax by ignoring irrelevant branches.
    - **AO***: AND-OR graph search for problem-solving with multiple possible solutions.
5. **Breadth-First Search (BFS) Algorithm**
    
    - **Advantage:** Guarantees shortest path in unweighted graphs.
    - **Disadvantage:** High memory usage.
6. **Real-Time Applications of Hill Climbing**
    
    - **Elevator Scheduling:** Determines optimal movement of elevators.
    - **Robot Path Planning:** Helps robots avoid obstacles efficiently.
7. **Optimizing Minimax in Real-Time Games**
    
    - **Techniques:**
        - **Alpha-Beta Pruning:** Cuts unnecessary computations.
        - **Iterative Deepening:** Searches depth levels progressively.
        - **Heuristic Evaluations:** Prioritizes better moves.
    - **Risks:** Over-pruning may ignore the best move, reducing accuracy.