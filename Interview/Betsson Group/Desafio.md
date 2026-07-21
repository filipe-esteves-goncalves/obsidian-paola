
**Assignment Details**
	The application project you have been given is for a legacy application that calculates the real time status of odds during a sporting event. During a sporting event, several aspects of the sport can be offered to be bet on, such as: **which team will win the match**, **how many points they will score** and **how many fouls they will incur**. 
	A bet has two pieces of information that might change during an event: its odds and its sell-in time. Essentially, these identify how advantageous the odd is and how much longer the odd will be valid for. 
	The legacy application retrieves the current version of odds from a server, calculates the new state for the odds and then displays them to the user. This is a bug fixing & refactoring task, where the goal is to restructure the project into a more easily maintainable app. 

**What to fix**:
 1.  Refactor the BetsRepository class, such that adding support for new bet types and their calculations is as easy as possible.
 2.  Add unit tests for BetsRepository. 
 3.  Refactor the Bets target using the MVVM architecture.

**What we look for** 
	Clean, readable, extendable and testable code. While the use of AI tools is something we embrace, to better evaluate your performance, they should not be used for completing this assignment

**Time & Delivery** 
	• Please submit the finished assignment in the form of zip file and reply to the HR’s email.
	• Make sure the project builds, even if the solution is not complete.

--------



## Prioritized refactoring plan

I inspected [BetsRepository.swift](vscode-file://vscode-app/Applications/Visual%20Studio%20Code.app/Contents/Resources/app/out/vs/code/electron-browser/workbench/workbench.html), [ViewController.swift](vscode-file://vscode-app/Applications/Visual%20Studio%20Code.app/Contents/Resources/app/out/vs/code/electron-browser/workbench/workbench.html), [Bet.swift](vscode-file://vscode-app/Applications/Visual%20Studio%20Code.app/Contents/Resources/app/out/vs/code/electron-browser/workbench/workbench.html), and [BetsCoreTests.swift](vscode-file://vscode-app/Applications/Visual%20Studio%20Code.app/Contents/Resources/app/out/vs/code/electron-browser/workbench/workbench.html). The main issue is not a single bug; it is that the business logic, UI flow, and data access are tightly coupled, which makes the app hard to extend and hard to test.

1. Priority 1 — Extract the odds-update rules from the repository into a rule-based engine

- Why: [BetsRepository.swift](vscode-file://vscode-app/Applications/Visual%20Studio%20Code.app/Contents/Resources/app/out/vs/code/electron-browser/workbench/workbench.html) contains a long, procedural block with hard-coded bet names, nested conditions, and magic logic. That violates the Open/Closed Principle and the Single Responsibility Principle.
- What to change:
    - Move each bet’s behavior into a dedicated rule or strategy, for example a `BetRule` protocol with implementations for “normal bets”, “Player performance”, “Total score”, and “Winning team”.
    - Keep the repository responsible only for orchestration: load bets, apply rules, save bets, return results.
- Benefit: adding a new bet type becomes a new rule implementation instead of editing a large conditional chain.

2. Priority 2 — Add unit tests for the repository behavior before touching the UI

- Why: [BetsCoreTests.swift](vscode-file://vscode-app/Applications/Visual%20Studio%20Code.app/Contents/Resources/app/out/vs/code/electron-browser/workbench/workbench.html) is empty, so there is no regression protection for the current logic. This is the highest-risk area because the rules are business-critical and currently unverified.
- What to change:
    - Add tests for:
        - normal bet decrement behavior,
        - special-case behavior for “Player performance” and “Total score”,
        - sell-in countdown behavior,
        - negative sell-in edge cases,
        - persistence round-trip through a fake service.
- Benefit: this makes the refactor safer and proves the rules are still correct after each change.

3. Priority 3 — Refactor the app target to MVVM

- Why: [ViewController.swift](vscode-file://vscode-app/Applications/Visual%20Studio%20Code.app/Contents/Resources/app/out/vs/code/electron-browser/workbench/workbench.html) is doing too much: it creates the collection view, manages loading animation, triggers async work, updates the model, and handles data source responsibilities. That is a clear violation of SRP and makes the view difficult to test.
- What to change:
    - Introduce an `OddsListViewModel` that owns:
        - loading state,
        - error state,
        - the list of displayed bets,
        - the async repository call.
    - Keep the view controller focused on UI binding and rendering only.
- Benefit: the view becomes thin, easier to test, and the business flow becomes reusable and more maintainable.

4. Priority 4 — Improve the domain model so it expresses behavior, not just data

- Why: [Bet.swift](vscode-file://vscode-app/Applications/Visual%20Studio%20Code.app/Contents/Resources/app/out/vs/code/electron-browser/workbench/workbench.html) is only a storage container. It does not encapsulate any business behavior, so the repository must know too much about how each bet should evolve.
- What to change:
    - Keep the model simple, but move transformation logic into dedicated rules or use an immutable domain object that is updated by the rule engine.
    - Avoid spreading business logic across the UI, repository, and service layers.
- Benefit: this aligns the code more closely with the Domain-Driven design principle and reduces hidden coupling.

5. Priority 5 — Introduce dependency inversion and testable service boundaries

- Why: [ViewController.swift](vscode-file://vscode-app/Applications/Visual%20Studio%20Code.app/Contents/Resources/app/out/vs/code/electron-browser/workbench/workbench.html) uses a concrete singleton service directly, which makes the app harder to isolate and test.
- What to change:
    - Inject the repository and service through initializers or a lightweight dependency container.
    - Keep the service behind the existing protocol abstraction in [BetsRepository.swift](vscode-file://vscode-app/Applications/Visual%20Studio%20Code.app/Contents/Resources/app/out/vs/code/electron-browser/workbench/workbench.html).
- Benefit: this follows the Dependency Inversion Principle and makes the app much easier to unit test with fakes.

6. Priority 6 — Replace ad-hoc UI state handling with explicit state and error presentation

- Why: the current loading flow simply prints an error and leaves the UI in an unclear state. That is fragile and not suitable for a production-quality app.
- What to change:
    - Introduce explicit states such as `.loading`, `.loaded`, and `.error`.
    - Show an error view or placeholder rather than relying on `print`.
- Benefit: the user experience becomes predictable, and the code becomes easier to reason about.

## Recommended execution order

I would implement them in this order:

1. Extract the bet rules from the repository.
2. Add repository tests around those rules.
3. Refactor the view controller into MVVM around the new view model.
4. Tighten dependency injection and error state handling.

If you want, I can start with the first item and implement it in a clean, testable way.

---------------

# 1. Extract the bet rules from repository :

What changed

- The odds update logic in [BetsRepository.swift](vscode-file://vscode-app/Applications/Visual%20Studio%20Code.app/Contents/Resources/app/out/vs/code/electron-browser/workbench/workbench.html) was extracted from one long procedural block into a rule-based structure.
- I introduced a small protocol, [BetsRepository.swift](vscode-file://vscode-app/Applications/Visual%20Studio%20Code.app/Contents/Resources/app/out/vs/code/electron-browser/workbench/workbench.html), so each bet behavior can be represented as an individual rule rather than being hard-coded inside the repository.
- I added regression-style tests in [BetsCoreTests.swift](vscode-file://vscode-app/Applications/Visual%20Studio%20Code.app/Contents/Resources/app/out/vs/code/electron-browser/workbench/workbench.html) that exercise:
    - a custom injected rule,
    - default bet decrement behavior,
    - special handling for “Player performance”,
    - expired “Total score” behavior.

Why this is the right first change

- The repository originally mixed orchestration, business rules, and conditional branching for specific bet names. That made it hard to extend and impossible to test cleanly.
- The new shape follows the Open/Closed Principle: to support a new bet type, you add a new rule rather than modify the repository’s core loop.

Verification

- I verified that the project still builds with:
    - xcodebuild build for the Bets scheme
- The build step completed without compiler errors.

What remains next

- The next step will be to continue the refactor by moving the UI logic in [ViewController.swift](vscode-file://vscode-app/Applications/Visual%20Studio%20Code.app/Contents/Resources/app/out/vs/code/electron-browser/workbench/workbench.html) into a view model and using the repository through a cleaner MVVM boundary.


## Overall assessment

From a tech lead perspective, I'd prioritize improvements in this order:

1. ✅ Replace string comparisons with a `BetType` enum.
2. ✅ Extract the update logic into small, focused methods.
3. ✅ Replace nested `if` chains with `switch` statements.
4. ✅ Encapsulate quality changes in helper methods (`increaseQuality`/`decreaseQuality`) to eliminate repeated boundary checks.
5. ✅ Introduce named constants for rule thresholds (`50`, `11`, `6`).
6. ✅ Separate orchestration (`updateOdds`) from business rules (`update`, `updateQuality`, `updateExpiredBet`, etc.).