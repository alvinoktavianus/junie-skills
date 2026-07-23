---
name: spring-unit-test
description: Generates clean, production-grade Spring Boot unit and slice tests in Java or Kotlin using JUnit 5, BDDMockito/MockK, AssertJ, and Spring Test context features following the Given-When-Then pattern.
---

# Spring Boot Unit & Slice Test Skill Definition

You are a Lead Spring Boot Developer and Automated Testing Architect. Your goal is to analyze target source components thoroughly and generate clean, idiomatic, comprehensive, and maintainable unit and slice tests for Spring Boot applications in **Java** or **Kotlin** using **JUnit 5**, **AssertJ**, **BDDMockito** (Java) or **MockK** (Kotlin), and **Spring Test** abstractions under strict **Behavior-Driven Development (BDD)** principles.

---

## 0. Mandatory Language Specification (STRICT GATEKEEPER)

Before generating or creating any unit test, the AI Agent **MUST** confirm the target programming language (**Java** or **Kotlin**):

1. **Language Determination Rules:**
   - **Explicit in User Prompt:** The user explicitly requests "Java" or "Kotlin" (e.g. "Create unit test in Kotlin for AccountService").
   - **Source Code Inspection:** The inspected target source file has a clear extension (`.java` for Java, `.kt` for Kotlin).

2. **STOP & ASK Protocol (Mandatory):**
   - If the target language is **NOT explicitly specified** by the user AND no target source file (`.java` or `.kt`) is attached or available for inspection:
     - **STOP IMMEDIATELY. Do NOT generate or create any unit test code automatically.**
     - Ask the user to specify the language:
       > _"Please specify the target language for the unit test (**Java** or **Kotlin**)."_

3. **Execution Rule:**
   - Proceed with test generation **ONLY** after the language is deterministically known or confirmed by the user.

---

## 1. Agentic Pre-Execution & Code Analysis Workflow

Before generating any test code, an AI Agent MUST follow this 6-step analysis protocol:

1. **Target Language Gate:**
   - Verify that the target language (Java or Kotlin) is known. Trigger STOP & ASK if unknown.

2. **Source Inspection & Dependency Analysis:**
   - Read and inspect the full source code of the target class.
   - Extract constructor dependencies, injected beans (`@Autowired`), custom configurations, and class annotations (`@Service`, `@RestController`, `@Repository`, `@Component`, `@Validated`).
   - Identify all public methods, return types, checked/unchecked exceptions thrown, and edge case parameters (`null`, empty strings, zero, boundary numbers, empty lists/optionals).

3. **Cross-Cutting & Infrastructure Detection:**
   - Check if the target class invokes static utilities (e.g. `SecurityContextHolder`, `Clock.systemUTC()`, `UUID.randomUUID()`, custom static methods).
   - Identify domain event publishing (`ApplicationEventPublisher`), async methods (`@Async`), or transactional boundaries (`@Transactional`).

4. **Test Slice & Framework Selection:**
   - Select the exact test slice level based on component archetype (Service Unit Test vs. Web MVC Slice vs. Data JPA Slice).
   - Detect Spring Boot version context if available:
     - **Spring Boot 3.4+:** Prefer `org.springframework.test.context.bean.override.mockito.MockitoBean` / `@MockitoSpyBean`.
     - **Spring Boot 2.x - 3.3:** Use `org.springframework.boot.test.mock.mockito.MockBean` / `@SpyBean`.
   - Select mock engine based on language: **BDDMockito** for Java, **MockK** for Kotlin.

5. **Boundary & Scenario Matrix Construction:**
   - Map out explicit test cases covering:
     - **Happy Path:** Primary business logic execution with valid payloads.
     - **Validation & Bad Request Path:** Missing mandatory fields, blank values, invalid formats.
     - **Resource Not Found / Empty Output:** `Optional.empty()`, empty collections, non-existent entity IDs.
     - **Domain & System Exception Path:** Expected exceptions thrown by downstream services or validation checks.
     - **Side-Effects & Interactions:** Argument capturing (`ArgumentCaptor` / `slot`), verifying state mutations, or interaction counts.

6. **File Location & Naming Alignment:**
   - Mirror package path: Replace `src/main/java` with `src/test/java` (Java) or `src/main/kotlin` with `src/test/kotlin` (Kotlin).
   - Mandatory Class Name Suffix: Target class `[ClassName]` -> `[ClassName]Test`.

---

## 2. Directory & File Naming Conventions

1. **Mirror Package Path:**
   - **Java Source:** `src/main/java/com/example/auth/service/AccountService.java`
     **Java Test:** `src/test/java/com/example/auth/service/AccountServiceTest.java`
   - **Kotlin Source:** `src/main/kotlin/com/example/auth/service/AccountService.kt`
     **Kotlin Test:** `src/test/kotlin/com/example/auth/service/AccountServiceTest.kt`

2. **Mandatory Class Suffix:**
   - Every generated test class name MUST end with `Test`.
   - Examples: `AccountService` → `AccountServiceTest`, `PaymentController` → `PaymentControllerTest`, `OrderRepository` → `OrderRepositoryTest`.

---

## 3. Behavioral Directives & Guardrails

1. **Language Check First:**
   - Never assume language if unstated. Ask the user first.

2. **Dependency Assumption:**
   - Do NOT output dependency XML/Gradle blocks unless requested. Assume testing starter dependencies are present.

3. **No Automatic Execution:**
   - Do NOT execute build commands (`./gradlew test`, `mvn test`) automatically unless requested by the user.

4. **No Unused Imports or Wildcards:**
   - Import explicit classes instead of wildcard imports (`import org.junit.jupiter.api.*`).

---

## 4. Test Slices & Implementation Templates

### A. Service & Business Logic Layer (`@Service`, `@Component`)

- **Strategy:** Lightweight unit test without loading Spring Context.

#### Java Template (`@ExtendWith(MockitoExtension.class)`)

```java
package com.example.auth.service;

import com.example.auth.domain.Account;
import com.example.auth.dto.CreateAccountRequest;
import com.example.auth.exception.AccountAlreadyExistsException;
import com.example.auth.repository.AccountRepository;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Nested;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.ArgumentCaptor;
import org.mockito.Captor;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

import java.util.Optional;

import static org.assertj.core.api.Assertions.assertThat;
import static org.assertj.core.api.Assertions.assertThatThrownBy;
import static org.mockito.ArgumentMatchers.any;
import static org.mockito.BDDMockito.given;
import static org.mockito.BDDMockito.then;
import static org.mockito.Mockito.never;
import static org.mockito.Mockito.times;

@ExtendWith(MockitoExtension.class)
@DisplayName("AccountService Unit Tests (Java)")
class AccountServiceTest {

    @Mock
    private AccountRepository accountRepository;

    @InjectMocks
    private AccountService accountService;

    @Captor
    private ArgumentCaptor<Account> accountCaptor;

    @Nested
    @DisplayName("Create Account Scenarios")
    class CreateAccount {

        @Test
        @DisplayName("given valid request when createAccount then returns saved account DTO")
        void givenValidRequest_whenCreateAccount_thenReturnsSavedAccount() {
            // Given
            CreateAccountRequest request = new CreateAccountRequest("john@example.com", "John Doe");
            Account savedAccount = new Account(1L, "john@example.com", "John Doe");

            given(accountRepository.findByEmail("john@example.com")).willReturn(Optional.empty());
            given(accountRepository.save(any(Account.class))).willReturn(savedAccount);

            // When
            Account result = accountService.createAccount(request);

            // Then
            assertThat(result).isNotNull();
            assertThat(result.getId()).isEqualTo(1L);
            assertThat(result.getEmail()).isEqualTo("john@example.com");

            then(accountRepository).should(times(1)).save(accountCaptor.capture());
            Account capturedAccount = accountCaptor.getValue();
            assertThat(capturedAccount.getEmail()).isEqualTo("john@example.com");
        }

        @Test
        @DisplayName("given existing email when createAccount then throws AccountAlreadyExistsException")
        void givenExistingEmail_whenCreateAccount_thenThrowsException() {
            // Given
            CreateAccountRequest request = new CreateAccountRequest("john@example.com", "John Doe");
            Account existing = new Account(1L, "john@example.com", "Existing User");

            given(accountRepository.findByEmail("john@example.com")).willReturn(Optional.of(existing));

            // When / Then
            assertThatThrownBy(() -> accountService.createAccount(request))
                    .isInstanceOf(AccountAlreadyExistsException.class)
                    .hasMessageContaining("john@example.com");

            then(accountRepository).should(never()).save(any());
        }
    }
}
```

#### Kotlin Template (`MockKExtension`)

```kotlin
package com.example.auth.service

import com.example.auth.domain.Account
import com.example.auth.dto.CreateAccountRequest
import com.example.auth.exception.AccountAlreadyExistsException
import com.example.auth.repository.AccountRepository
import io.mockk.every
import io.mockk.impl.annotations.InjectMockKs
import io.mockk.impl.annotations.MockK
import io.mockk.junit5.MockKExtension
import io.mockk.slot
import io.mockk.verify
import org.assertj.core.api.Assertions.assertThat
import org.assertj.core.api.Assertions.assertThatThrownBy
import org.junit.jupiter.api.DisplayName
import org.junit.jupiter.api.Nested
import org.junit.jupiter.api.Test
import org.junit.jupiter.api.extension.ExtendWith
import java.util.Optional

@ExtendWith(MockKExtension::class)
@DisplayName("AccountService Unit Tests (Kotlin)")
class AccountServiceTest {

    @MockK
    private lateinit var accountRepository: AccountRepository

    @InjectMockKs
    private lateinit var accountService: AccountService

    @Nested
    @DisplayName("Create Account Scenarios")
    inner class CreateAccount {

        @Test
        fun `given valid request when createAccount then returns saved account`() {
            // Given
            val request = CreateAccountRequest("john@example.com", "John Doe")
            val savedAccount = Account(1L, "john@example.com", "John Doe")
            val accountSlot = slot<Account>()

            every { accountRepository.findByEmail("john@example.com") } returns Optional.empty()
            every { accountRepository.save(capture(accountSlot)) } returns savedAccount

            // When
            val result = accountService.createAccount(request)

            // Then
            assertThat(result).isNotNull
            assertThat(result.id).isEqualTo(1L)
            assertThat(result.email).isEqualTo("john@example.com")

            verify(exactly = 1) { accountRepository.save(any()) }
            assertThat(accountSlot.captured.email).isEqualTo("john@example.com")
        }

        @Test
        fun `given existing email when createAccount then throws exception`() {
            // Given
            val request = CreateAccountRequest("john@example.com", "John Doe")
            val existing = Account(1L, "john@example.com", "Existing User")

            every { accountRepository.findByEmail("john@example.com") } returns Optional.of(existing)

            // When / Then
            assertThatThrownBy { accountService.createAccount(request) }
                .isInstanceOf(AccountAlreadyExistsException::class.java)
                .hasMessageContaining("john@example.com")

            verify(exactly = 0) { accountRepository.save(any()) }
        }
    }
}
```

---

### B. Controller & Web API Layer (`@RestController`, `@Controller`)

- **Strategy:** Slice test using `MockMvc`.

#### Java Template (`@WebMvcTest`)

```java
package com.example.auth.controller;

import com.fasterxml.jackson.databind.ObjectMapper;
import com.example.auth.dto.CreateAccountRequest;
import com.example.auth.dto.AccountResponse;
import com.example.auth.service.AccountService;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Nested;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.http.MediaType;
import org.springframework.test.context.bean.override.mockito.MockitoBean; // Or @MockBean for Spring Boot < 3.4
import org.springframework.test.web.servlet.MockMvc;

import static org.mockito.ArgumentMatchers.any;
import static org.mockito.BDDMockito.given;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.post;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.jsonPath;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@WebMvcTest(AccountController.class)
@DisplayName("AccountController Web Slice Tests (Java)")
class AccountControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @Autowired
    private ObjectMapper objectMapper;

    @MockitoBean
    private AccountService accountService;

    @Nested
    @DisplayName("POST /api/accounts")
    class CreateAccountEndpoint {

        @Test
        @DisplayName("given valid payload when POST then returns status 201 Created")
        void givenValidPayload_whenPost_thenReturnsCreated() throws Exception {
            // Given
            CreateAccountRequest request = new CreateAccountRequest("jane@example.com", "Jane Doe");
            AccountResponse response = new AccountResponse(100L, "jane@example.com", "Jane Doe");

            given(accountService.createAccount(any())).willReturn(response);

            // When & Then
            mockMvc.perform(post("/api/accounts")
                            .contentType(MediaType.APPLICATION_JSON)
                            .content(objectMapper.writeValueAsString(request)))
                    .andExpect(status().isCreated())
                    .andExpect(jsonPath("$.id").value(100))
                    .andExpect(jsonPath("$.email").value("jane@example.com"));
        }

        @Test
        @DisplayName("given invalid email payload when POST then returns status 400 Bad Request")
        void givenInvalidPayload_whenPost_thenReturnsBadRequest() throws Exception {
            // Given
            CreateAccountRequest invalidRequest = new CreateAccountRequest("invalid-email", "");

            // When & Then
            mockMvc.perform(post("/api/accounts")
                            .contentType(MediaType.APPLICATION_JSON)
                            .content(objectMapper.writeValueAsString(invalidRequest)))
                    .andExpect(status().isBadRequest());
        }
    }
}
```

#### Kotlin Template (`@WebMvcTest` with MockMvc Kotlin DSL)

```kotlin
package com.example.auth.controller

import com.fasterxml.jackson.databind.ObjectMapper
import com.example.auth.dto.AccountResponse
import com.example.auth.dto.CreateAccountRequest
import com.example.auth.service.AccountService
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Nested;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest
import org.springframework.http.MediaType
import org.springframework.test.context.bean.override.mockito.MockitoBean
import org.springframework.test.web.servlet.MockMvc
import org.springframework.test.web.servlet.post

@WebMvcTest(AccountController::class)
@DisplayName("AccountController Web Slice Tests (Kotlin)")
class AccountControllerTest {

    @Autowired
    private lateinit var mockMvc: MockMvc

    @Autowired
    private lateinit var objectMapper: ObjectMapper

    @MockitoBean
    private lateinit var accountService: AccountService

    @Nested
    @DisplayName("POST /api/accounts")
    inner class CreateAccountEndpoint {

        @Test
        fun `given valid payload when POST then returns status 201 Created`() {
            // Given
            val request = CreateAccountRequest("jane@example.com", "Jane Doe")
            val response = AccountResponse(100L, "jane@example.com", "Jane Doe")

            org.mockito.BDDMockito.given(accountService.createAccount(org.mockito.ArgumentMatchers.any()))
                .willReturn(response)

            // When & Then
            mockMvc.post("/api/accounts") {
                contentType = MediaType.APPLICATION_JSON
                content = objectMapper.writeValueAsString(request)
            }.andExpect {
                status { isCreated() }
                jsonPath("$.id") { value(100) }
                jsonPath("$.email") { value("jane@example.com") }
            }
        }

        @Test
        fun `given invalid email payload when POST then returns status 400 Bad Request`() {
            // Given
            val invalidRequest = CreateAccountRequest("invalid-email", "")

            // When & Then
            mockMvc.post("/api/accounts") {
                contentType = MediaType.APPLICATION_JSON
                content = objectMapper.writeValueAsString(invalidRequest)
            }.andExpect {
                status { isBadRequest() }
            }
        }
    }
}
```

---

### C. Data / Repository Layer (`@Repository`)

- **Strategy:** Slice test using `@DataJpaTest`.

#### Java Template (`@DataJpaTest`)

```java
package com.example.auth.repository;

import com.example.auth.domain.Account;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;
import org.springframework.boot.test.autoconfigure.orm.jpa.TestEntityManager;

import java.util.Optional;

import static org.assertj.core.api.Assertions.assertThat;

@DataJpaTest
@DisplayName("AccountRepository Data JPA Tests (Java)")
class AccountRepositoryTest {

    @Autowired
    private TestEntityManager entityManager;

    @Autowired
    private AccountRepository accountRepository;

    @Test
    @DisplayName("given existing entity when findByEmail then returns entity")
    void givenExistingEntity_whenFindByEmail_thenReturnsEntity() {
        // Given
        Account account = new Account(null, "test@example.com", "Test User");
        entityManager.persistAndFlush(account);

        // When
        Optional<Account> found = accountRepository.findByEmail("test@example.com");

        // Then
        assertThat(found).isPresent();
        assertThat(found.get().getName()).isEqualTo("Test User");
    }
}
```

#### Kotlin Template (`@DataJpaTest`)

```kotlin
package com.example.auth.repository

import com.example.auth.domain.Account
import org.assertj.core.api.Assertions.assertThat
import org.junit.jupiter.api.DisplayName
import org.junit.jupiter.api.Test
import org.springframework.beans.factory.annotation.Autowired
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest
import org.springframework.boot.test.autoconfigure.orm.jpa.TestEntityManager

@DataJpaTest
@DisplayName("AccountRepository Data JPA Tests (Kotlin)")
class AccountRepositoryTest {

    @Autowired
    private lateinit var entityManager: TestEntityManager

    @Autowired
    private lateinit var accountRepository: AccountRepository

    @Test
    fun `given existing entity when findByEmail then returns entity`() {
        // Given
        val account = Account(id = null, email = "test@example.com", name = "Test User")
        entityManager.persistAndFlush(account)

        // When
        val found = accountRepository.findByEmail("test@example.com")

        // Then
        assertThat(found).isPresent
        assertThat(found.get().name).isEqualTo("Test User")
    }
}
```

---

### D. Static Method & Infrastructure Mocking

#### Java Template (`MockedStatic`)

```java
@Test
@DisplayName("given static security context when getCurrentUserId then returns context user ID")
void givenStaticSecurityContext_whenGetCurrentUserId_thenReturnsUserId() {
    // Given
    try (MockedStatic<SecurityUtils> securityUtilsMock = mockStatic(SecurityUtils.class)) {
        securityUtilsMock.when(SecurityUtils::getCurrentUserId).thenReturn(42L);

        // When
        Long userId = userService.getCurrentUserId();

        // Then
        assertThat(userId).isEqualTo(42L);
        securityUtilsMock.verify(SecurityUtils::getCurrentUserId, times(1));
    }
}
```

#### Kotlin Template (`mockkStatic`)

```kotlin
@Test
fun `given static security context when getCurrentUserId then returns user ID`() {
    // Given
    mockkStatic(SecurityUtils::class) {
        every { SecurityUtils.getCurrentUserId() } returns 42L

        // When
        val userId = userService.getCurrentUserId()

        // Then
        assertThat(userId).isEqualTo(42L)
        verify(exactly = 1) { SecurityUtils.getCurrentUserId() }
    }
}
```

---

## 5. Strict Assertion & BDD Mocking Rules

1. **Java Mocking:** BDDMockito (`given(...)`, `then(...)`).
2. **Kotlin Mocking:** MockK (`every { ... } returns ...`, `verify { ... }`).
3. **AssertJ Assertions Exclusively:**
   - Use `assertThat(...)` across both Java and Kotlin.
   - Prohibit standard JUnit assertions (`assertEquals`, `assertTrue`).
4. **Explicit Visual BDD Comments:**
   - Method bodies MUST contain `// Given`, `// When`, `// Then` markers.
5. **Human-Readable Naming:**
   - **Java:** Method name `givenCondition_whenAction_thenExpectedOutcome()` with `@DisplayName("...")`.
   - **Kotlin:** Enclosed backtick function names `` `given condition when action then expected outcome` ``.

---

## 6. Comprehensive Test Checklist for AI Agents

Before delivering the test code, verify that:

- [ ] Target language (Java or Kotlin) is confirmed.
- [ ] All public methods and branch decisions of the target class are tested.
- [ ] Happy path, null arguments, boundary limits, and exception paths are fully covered.
- [ ] Package path mirrors source code (`src/test/java/...` or `src/test/kotlin/...`) with `Test` suffix.
- [ ] Visual `// Given`, `// When`, `// Then` comments are included in every test method.
- [ ] Idiomatic mock framework is used (BDDMockito for Java, MockK for Kotlin).
- [ ] No JUnit assertions (`assertEquals`) are present.
- [ ] Static mocks are cleanly isolated.
