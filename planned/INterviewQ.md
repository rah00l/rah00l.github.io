


**Ruby and Rails Interview Questions & Answers**

---

### 1. **Ruby - Debugging and Troubleshooting**
**Q:** How do you handle unexpected nil errors in Ruby?
**A:** Use safe navigation (`&.`), presence checks (`nil?`, `blank?`), and wrap logic with guard clauses.

**Q:** What’s your approach for debugging intermittent issues in a long-running Ruby service?
**A:** Add granular logs, capture runtime state using `binding.irb` or `puts`, monitor memory/CPU usage, and replicate conditions in test environments.

**Q:** How do you handle exceptions in a resilient Ruby script?
**A:** Use `begin-rescue`, log detailed error info, retry for transient errors, and alert for persistent ones.

---

### 2. **Ruby - Code Quality and Maintenance**
**Q:** How do you write maintainable Ruby code?
**A:** Follow SOLID principles, avoid deep nesting, prefer small methods and service objects.

**Q:** What’s your strategy for managing large modules?
**A:** Break into smaller modules or mixins, use namespacing, and limit coupling.

**Q:** How do you refactor duplicated Ruby logic?
**A:** Extract to helper methods, modules, or use DRY abstractions like lambdas or shared service objects.

---

### 3. **Rails - Request Handling & Controller Design**
**Q:** How do you prevent large controller actions in Rails?
**A:** Move logic to services, use `before_action` filters smartly, and keep controllers RESTful.

**Q:** How do you handle conditional rendering in controllers?
**A:** Use `respond_to`, `head`, or service-driven decorators for clean output rendering.

**Q:** What are strong parameters and why are they important?
**A:** They whitelist permitted params to prevent mass-assignment vulnerabilities.

---

### 4. **Rails - Model Optimization**
**Q:** How do you handle large or complex model methods?
**A:** Extract logic into scopes, class methods, or move to service/interactor classes.

**Q:** What’s your approach for validating dependent attributes?
**A:** Use custom validations with conditionals and add test coverage for edge cases.

**Q:** How do you handle dirty tracking or change detection in models?
**A:** Use ActiveModel's `attribute_changed?`, `saved_change_to_attribute`, and callbacks.

---

### 5. **Rails - Frontend Integration & APIs**
**Q:** How do you structure a Rails API response?
**A:** Use serializers like `ActiveModel::Serializer` or `Jbuilder`, ensure consistent formats.

**Q:** How do you handle authentication for APIs?
**A:** Use JWT or OAuth with middleware, and enforce authorization at the controller level.

**Q:** How do you handle versioning in Rails APIs?
**A:** Namespace routes (e.g., `api/v1`) and separate controllers/serializers by version.

---

### 6. **Rails - Security and Resilience**
**Q:** How do you prevent SQL Injection in Rails?
**A:** Use ActiveRecord methods, parameterized queries, and avoid string interpolation in queries.

**Q:** How do you prevent CSRF in Rails?
**A:** Enable the authenticity token mechanism and use `protect_from_forgery` in controllers.

**Q:** How do you secure sensitive credentials?
**A:** Use Rails credentials (`config/credentials.yml.enc`), and never commit secrets to version control.

---

### 7. **Rails - Real-world Scenarios & Debugging**
**Q:** How do you debug a delayed Sidekiq job not being executed?
**A:** Check job queues, logs, retry/delay settings, Redis connection, and background processor status.

**Q:** How do you monitor a memory-intensive Rails process?
**A:** Use `ps`, `top`, or `htop`, check logs for GC stats, and use tools like New Relic or Scout.

**Q:** How do you recover a stuck deployment or migration?
**A:** Roll back or fix the migration manually, investigate locks, and use transactional safety mechanisms.

---

Let me know if you'd like questions for Docker, AWS, or frontend tech like Angular next!

