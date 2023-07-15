## Embracing Orthogonality: Unleashing the Power of Software Architecture

_"A well-designed software system embraces orthogonality, allowing individual components to evolve independently,
minimizing ripple effects." - Martin Fowler_

A good software architecture should have its spirit; for those developers who are working on it, they should feel
there's a raw power given by the architecture to empower them to develop, scale and evolve the system.

This spirit stems from the meticulous efforts of architects who craft their masterpieces with careful consideration of
various concepts, such as technology selection, API design, and data flow.

While many concepts are taught and discussed, one integral aspect often overlooked is orthogonality—a critical element
in fostering this powerful spirit within software systems. In computing, orthogonality signifies a kind of independence
or decoupling between components, ensuring that changes made to one system have no impact on another.

### the Essence of Orthogonality

To grasp the concept of orthogonality, let's begin with its geometric definition: when two lines meet at right angles,
like the x-axis and y-axis, moving along the x-axis only changes the x-coordinate, leaving the y-coordinate unaffected,
and vice versa.

Translating this geometric principle to software system design, orthogonality refers to the independence or decoupling
between two systems. When we say that two systems are orthogonal, it signifies that modifying one system will have no
impact on the other, akin to the perpendicular lines in geometry remaining unaffected when either one shifts.

### why we need orthogonality

Alright, imagine this: you're boarding a "futuristic" plane that's equipped with more buttons than a spaceship's control
panel. You sit back, relax, and decide to play a little game of "Press the Button." You tap just one innocent-looking
button, and boom! It's like you've set off a domino effect, and suddenly, every other button on the plane decides to
join the party! Chaos ensues, lights flashing, alarms blaring, and the pilots sweating buckets trying to figure out what
you've done.

Believe me, my friends, that's NOT the kind of roller coaster ride you want when you're flying high in the sky. We need
our pilots to focus on flying, not solving button-induced puzzles! So, when it comes to software systems, we follow the
same rule: no "button-induced" madness! We want each control input to have its job and not mess with others.

### benefits of orthogonality:

- reduce risk:
    - Enhanced System Robustness: Orthogonal design shields the entire system from being compromised by a single sick
      component. When a specific
      module encounters issues, the impact remains localized, preventing widespread system failures. This fortification
      results in a more resilient architecture, where changes made to one isolated component do not reverberate across
      the
      entire system. As a result, our software becomes more stable and less prone to catastrophic failures;

    - Streamlined Debugging and Diagnosing: Embracing orthogonality provides developers with an advantageous
      perspective—having a clear view and monitoring capabilities over individual, independent, and smaller subsystems.
      This
      logical division simplifies the process of identifying the root cause of issues. Just as a seasoned detective
      narrows
      down suspects one by one, developers can focus their efforts on specific components, leading to quicker and more
      accurate debugging and diagnosis.

    - Efficient Testing and Confident Integration: By isolating components within an orthogonal system, testing becomes
      highly targeted and efficient. Developers can concentrate on verifying the functionality of each individual module
      without the need to engage the entire system. This focused approach ensures that each component functions reliably
      before integration. Thus, when the time comes to assemble the system, developers can do so with confidence,
      knowing that
      the thoroughly tested and self-sufficient modules will harmoniously integrate into the whole.

    - Independence from Vendors and Third-Party Dependencies: Orthogonal design liberates the system from being shackled
      to specific vendors or third-party dependencies. By isolating interfaces and interactions, our architecture
      becomes
      agnostic to external services or technologies. This freedom allows us to adapt to changes in the technological
      landscape, explore different vendors, or pivot to alternative solutions without disrupting the core development,
      ensuring a more flexible and future-proof system.

- gain productivity and freedom
    - Self-Contained Components: Streamlined Development and Testing: Adopting self-contained components with
      well-defined purposes is like having focused superheroes in your software arsenal. Each component operates
      independently, reducing the stress and anxiety of making changes to a partial system. Developers can confidently
      modify or enhance a specific component without worrying about causing a ripple effect throughout the entire
      system. This localized impact expedites development time and testing efforts, as developers can concentrate solely
      on the task at hand, rather than fretting over hundreds of other interconnected elements.
    - Promoting Component Reuse: The Power of Loosely Coupled Systems:  Loosely coupled systems, enabled by
      orthogonality, allow for seamless reconfiguration and reengineering. Just like grabbing useful components from
      your old car and fitting them perfectly into your new ride, orthogonality liberates developers from the shackles
      of rigid dependencies. The result is a more versatile and adaptable system, ready to take on various challenges
      without reinventing the wheel.
    - Freedom of Individual Implementations: Unleashing Developer Creativity: In the realm of orthogonality, developers
      revel in the freedom to express their creativity without being bound to a monolithic approach. Want to write one
      component in Rust and another in Golang? No problem! Embracing diverse tools for different tasks becomes a breeze,
      fostering an environment where individual implementations can shine. This freedom sparks innovation and empowers
      developers to select the best tools for each specific job.
    - The Power of Amplification: Combining orthogonal components results in a fascinating synergy. Imagine one
      component being able to perform M tasks and another orthogonal component complementing it with N additional
      abilities. The result? An explosive M * N capabilities! Orthogonal design maximizes the potential of each
      component, making the whole system far greater than the sum of its parts.
    - Enjoying Peaceful Coding Solitude: Embracing orthogonality allows developers to work on their own modules with
      ease, without having to grapple with the challenges of meddling colleagues. So, developers, rejoice in the
      blissful coding solitude offered by orthogonality! (take this for fun)

### How to design and achieve orthogonality

- Assessing the Impact of Module Changes: The One-Module Rule: When making dynamic changes to a module within the
  system, always ask yourself a crucial question: How many modules are affected by this change? The golden answer should
  always be one! Consider a simple example: altering the positioning of a component in the graphical user interface (
  GUI). This change should not cascade into having to fix the API or modify the database schema. Embracing the
  one-module rule ensures that modifications remain localized and don't trigger a chain reaction throughout the entire
  system.

- Introducing Layered Components: A Cohesive Structure: A well-designed system is akin to a harmonious symphony, where
  each instrument plays its part. To achieve this harmony, consider introducing layered components. For instance, when
  multiple modules interact with a database, creating a middle layer responsible for handling requests and database
  interactions is a stroke of brilliance. This intermediary acts as a protective shield, preventing changes to the
  database schema from affecting all the modules. Instead, only the middle layer module requires adjustments, creating a
  more manageable and cohesive architecture.

- Avoiding Over-reliance on External Systems: Steer Clear of Rippling Effects: Picture your system as a resilient
  island, self-sufficient and unaffected by the turbulent waves of external changes. When it comes to external systems,
  be cautious not to become overly dependent. If a relied-upon external system experiences changes or crashes, the
  impact can ripple through your entire system, causing disruption. By keeping a healthy distance and maintaining
  independence, your system remains stable and robust.

- Keep your codes SHY:  Encourage your modular systems not to rely on the internal implementations of other modules.
  Instead, favor a more elegant approach where objects take charge of their own states. This design principle ensures a
  clean separation of concerns, leading to a more maintainable and flexible system.

- Keep your codes DRY (don't repeat yourself): A codebase should sparkle like a gem, free from redundant and repetitive
  elements. Avoid the quagmire of duplicated functions. When similar functions share common logic, resist the temptation
  to change them individually. Instead, extract the shared logic into a single, shared function. This refactoring not
  only improves code maintainability but also minimizes the effort needed to fix issues, as changes only need to be made
  once.

- Testing in Orthogonal Design: A Playful Dance of Confidence: In the realm of orthogonal design, testing becomes an
  engaging dance of confidence. Utilize mocking systems or data flows to test modules in isolation, while interacting
  with other orthogonal components. This focused testing approach ensures that each module gains the spotlight and
  proves its capabilities with unwavering certainty. As each module passes its tests with flying colors, the grand
  integration dance becomes a joyous celebration of a well-orchestrated system.

### Conclusion

embracing orthogonality elevates software architecture to a level of resilience, adaptability, and maintainability that
transcends conventional systems. It empowers developers to navigate through challenges with confidence, knowing that
their meticulously designed components will continue to function independently, like harmonious gears in a well-oiled
machine.
