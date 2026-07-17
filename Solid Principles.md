---
cssclasses:
  - wide-page
---

| **Letter**     | **Principle**             | **Formal Definition**                                                                                      | **Plain English Goal**                                                                                                                             |
| -------------- | ------------------------- | ---------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| **S**<br>(SRP) | **Single Responsibility** | A class should have one, and only one, reason to change.                                                   | **One class = one job.** <br>Keep your components focused and highly cohesive.                                                                     |
| **O**<br>(OCP) | **Open/Closed**           | Software entities should be open for extension, but closed for modification.                               | **Add features without touching old code.** <br>Write code so you can add new functionality by adding new classes, <br>not altering existing ones. |
| **L**<br>(LSP) | **Liskov Substitution**   | Subtypes must be substitutable for their base types without altering program correctness.                  | **Don't break inheritance.** <br>If you swap a parent class for its child class, <br>the application shouldn't crash or behave weirdly.            |
| **I**<br>(ISP) | **Interface Segregation** | Clients should not be forced to depend upon interfaces that they do not use.                               | **Keep interfaces lean.** <br>It's better to have many small, specific interfaces <br>than one massive, generic one.                               |
| **D**<br>(DIP) | **Dependency Inversion**  | Depend on abstractions, not on concretions. <br>High-level modules should not depend on low-level modules. | **Decouple your system.** <br>Business logic should rely on interfaces,<br>not on specific database or API implementations.                        |
