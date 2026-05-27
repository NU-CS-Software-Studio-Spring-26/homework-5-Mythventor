# HW5 Submission

## Links

- **Main repo:** https://github.com/NU-CS-Software-Studio-Spring-26/homework-5-Mythventor
- **.cursorignore:** https://github.com/NU-CS-Software-Studio-Spring-26/homework-5-Mythventor/blob/main/.cursorignore
- **AGENTS.md:** https://github.com/NU-CS-Software-Studio-Spring-26/homework-5-Mythventor/blob/main/AGENTS.md
- **.cursor/rules/rails-conventions.mdc:** https://github.com/NU-CS-Software-Studio-Spring-26/homework-5-Mythventor/blob/main/.cursor/rules/rails-conventions.mdc
- **.cursor/rules/security.mdc:** https://github.com/NU-CS-Software-Studio-Spring-26/homework-5-Mythventor/blob/main/.cursor/rules/security.mdc

---

## Part 3

### Ask Mode

**Prompt:**
Where in this codebase is the todo creation currently implemented? Cite the exact files and line numbers. Do not propose changes.

**Cursor's Answer:**

Todo creation is implemented in these places:

- config/routes.rb line 2: resources :todos defines POST /todos routed to TodosController#create
- app/controllers/todos_controller.rb lines 22-35: the create action builds a Todo from permitted params, saves it, then redirects or re-renders
- app/controllers/todos_controller.rb lines 73-76: strong params allow only description during creation/update
- app/views/todos/new.html.erb line 5: the new page renders the shared form with @todo
- app/views/todos/_form.html.erb lines 1-22: form_with(model: todo) generates the create form including the description field and submit button

**Verification:**
I opened each cited file and confirmed the line numbers are real and accurate. No hallucinated paths.

---

### Plan Mode

**Prompt:**
I want to change todo creation so that a todo is automatically associated with the user who created it. Propose a plan as a numbered list of changes, including files to edit, new tests to add, and any migration. Do not write code.

**Cursor's Plan:**

1. Generate migration adding required user reference to todos
2. Add Todo-to-User association in app/models/todo.rb
3. Build new todos through current_user.todos and keep user_id out of strong params in todos_controller.rb
4. Update todo/user fixtures for the required association
5. Add model and controller tests for ownership assignment and parameter protection
6. Run migrations and Rails tests

**My Edits:**
I removed step 4 (updating fixtures) since there is no User model in this app yet — updating fixtures would cause test failures. I also narrowed step 3 to creation only, skipping the index/set_todo ownership scoping since the assignment only asks for creation ownership.

---

### Agent Mode

**Prompt:**
Implement only step 1 of the plan: generate a migration called AddUserRefToTodos that adds a user_id integer column to the todos table with an index. Do not add a foreign key constraint since there is no users table yet. Do not touch any other files — not the model, not the controller, not any tests.

**Commit:** https://github.com/NU-CS-Software-Studio-Spring-26/homework-5-Mythventor/commit/71a9990c7ef2d2fa3bde4e8bcb767d2ab9db9cac

---

### Bad → Good Prompt Rewrite

**Bad prompt:**
fix the bug in todos

**Good prompt:**

Context: app/controllers/todos_controller.rb (create action), app/models/todo.rb

Task: The create action does not associate the new todo with the current user — it builds directly from Todo.new instead of current_user.todos.build

Expected: A newly created todo should have its user_id set to the current user's id

Actual: todo.user_id is nil after creation since user_id is not set anywhere in the create flow

Constraints: Only touch todos_controller.rb and todo.rb; no new gems; follow the existing before_action and strong params pattern

Done when: A controller test confirms that after a POST to /todos, the created todo's user_id equals the signed-in user's id

---

## Part 4

### Turbo Streams Explanation

A Turbo Stream is a Rails/Hotwire response that sends DOM update instructions to the browser instead of a full HTML page. Rather than reloading the whole page, the server sends small turbo-stream elements that tell the browser to append, replace, remove, etc. a specific element on the current page.

Key differences from a normal HTML response:
- Normal HTML response MIME type: text/html (replaces the whole page)
- Turbo Stream MIME type: text/vnd.turbo-stream.html (mutates specific elements only)

Controller pattern:
respond_to do |format|
  format.html { redirect_to todos_path }
  format.turbo_stream
end

View file convention:
The matching view lives at app/views/<controller>/<action>.turbo_stream.erb.
For the toggle_priority action that means: app/views/todos/toggle_priority.turbo_stream.erb

The 7 Turbo Stream actions: append, prepend, replace, update, remove, before, after.

What I verified against the Turbo Streams handbook (https://turbo.hotwired.dev/):
- Confirmed the MIME type is text/vnd.turbo-stream.html
- Confirmed all 7 actions exist and match the handbook's documentation
- Confirmed the view filename convention action.turbo_stream.erb matches the official Rails guide
- The app currently has no existing Turbo Stream responses — all controller actions only respond with format.html and format.json

### Part 4 Pull Request

**PR URL:** https://github.com/NU-CS-Software-Studio-Spring-26/homework-5-Mythventor/pull/1