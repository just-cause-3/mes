# BCS703 — Module 2: Public-Key Cryptography
## Exam-Ready Questions & Answers

> Covers: Pseudorandom Number Generators, Public-Key Cryptography principles, RSA, Diffie-Hellman, and Elliptic Curve Cryptography.

---

## Q1. Explain the RSA algorithm and perform encryption/decryption with an example.

### Introduction
RSA, named after its inventors **Rivest, Shamir, and Adleman (1977)**, is the best-known and most widely used public-key (asymmetric) cryptosystem. It is also a **block cipher**, where plaintext and ciphertext are integers between 0 and n − 1 for some n. Its security rests on the difficulty of factoring the product of two large prime numbers.

For a plaintext block M and ciphertext block C:

```
Encryption:  C = M^e mod n
Decryption:  M = C^d mod n = (M^e)^d mod n = M^(ed) mod n
```

- Public key: **PU = {e, n}**
- Private key: **PR = {d, n}**

Both sender and receiver know n. The sender knows e; only the receiver knows d.

### Key Generation Steps (by Alice)
1. Select two distinct large prime numbers **p** and **q** (p ≠ q).
2. Calculate **n = p × q**.
3. Calculate Euler's totient **φ(n) = (p − 1)(q − 1)**.
4. Select an integer **e** such that **gcd(φ(n), e) = 1** and **1 < e < φ(n)**.
5. Calculate **d** such that **d ≡ e⁻¹ (mod φ(n))**, i.e. **(d × e) mod φ(n) = 1**.
6. Publish the public key **PU = {e, n}**.
7. Keep the private key **PR = {d, n}** secret.

### Worked Example (p = 17, q = 11)
1. Select primes: p = 17, q = 11
2. n = p × q = 17 × 11 = **187**
3. φ(n) = (17 − 1)(11 − 1) = 16 × 10 = **160**
4. Choose e such that gcd(160, e) = 1 → **e = 7**
5. Find d: (d × 7) mod 160 = 1 → **d = 23** (since 23 × 7 = 161 = 1 mod 160)
6. Public key PU = {7, 187}
7. Private key PR = {23, 187}

**Given message M = 88:**

```
Encryption:  C = 88^7 mod 187 = 11
Decryption:  M = 11^23 mod 187 = 88
```

### Alternative Small Example (p = 3, q = 11) — easy to compute by hand
- n = 3 × 11 = 33
- φ(n) = (3 − 1)(11 − 1) = 2 × 10 = 20
- e = 7 (gcd(20, 7) = 1)
- d = 3 (since 7 × 3 = 21 ≡ 1 mod 20)
- PU = {7, 33}, PR = {3, 33}
- Take M = 31:
  - Encryption: C = 31⁷ mod 33 = **4**
  - Decryption: M = 4³ mod 33 = 64 mod 33 = **31** ✓

### Computation tip (modular exponentiation)
Use the property `[(a mod n) × (b mod n)] mod n = (a × b) mod n` to keep intermediate numbers small instead of computing the full power.

---

## Q2. Distinguish between Conventional (Symmetric) and Public-Key (Asymmetric) Cryptography.

| Aspect | Conventional Encryption | Public-Key Encryption |
|--------|------------------------|----------------------|
| **Algorithm & key** | The **same** algorithm with the **same key** is used for both encryption and decryption. | **One** algorithm is used for encryption and a **related** algorithm for decryption, with a **pair of keys** (one for each). |
| **Key sharing** | Sender and receiver must **share** the algorithm and the key. | Sender and receiver must each have **one of the matched pair** of keys (not the same one). |
| **Key secrecy (security)** | The key **must be kept secret**. | **One of the two keys** must be kept secret. |
| **Decipherability** | Must be impossible/impractical to decipher a message if the key is kept secret. | Must be impossible/impractical to decipher a message if one of the keys is kept secret. |
| **Knowledge attack** | Knowledge of the algorithm + samples of ciphertext must be insufficient to determine the key. | Knowledge of the algorithm + one of the keys + ciphertext samples must be insufficient to determine the other key. |
| **Number of keys** | 1 (shared secret key). | 2 (public + private key pair). |
| **Speed** | Faster. | Slower (based on heavy mathematical functions). |
| **Examples** | DES, AES, Blowfish. | RSA, Diffie-Hellman, ECC. |

**Key insight:** Public-key cryptography solves the two big problems of symmetric systems — **key distribution** (no need to securely share a secret key in advance) and **digital signatures** (verifying the sender).

---

## Q3. Write the requirements for Public-Key Cryptography.

A public-key cryptosystem must satisfy the following six conditions (formulated by Diffie and Hellman):

1. It is **computationally easy** for a party B to generate a key pair (public key PUᵦ, private key PRᵦ).

2. It is **computationally easy** for a sender A, knowing PUᵦ and the message M, to generate the ciphertext:
   ```
   C = E(PUᵦ, M)
   ```

3. It is **computationally easy** for the receiver B to decrypt the ciphertext using the private key:
   ```
   M = D(PRᵦ, C) = D[PRᵦ, E(PUᵦ, M)]
   ```

4. It is **computationally infeasible** for an adversary, knowing the public key PUᵦ, to determine the private key PRᵦ.

5. It is **computationally infeasible** for an adversary, knowing PUᵦ and ciphertext C, to recover the original message M.

6. **(Optional, for signatures)** The two keys can be applied in **either order**:
   ```
   M = D[PUᵦ, E(PRᵦ, M)] = D[PRᵦ, E(PUᵦ, M)]
   ```

### Trap-door One-Way Function
A practical public-key scheme depends on a suitable **trap-door one-way function**:

- A **one-way function** is one where:
  - Y = f(X) is **easy** to compute
  - X = f⁻¹(Y) is **infeasible** to compute

- A **trap-door one-way function** is a family of invertible functions f_k such that:
  - Y = f_k(X) is **easy**, if k and X are known
  - X = f_k⁻¹(Y) is **easy**, if k and Y are known
  - X = f_k⁻¹(Y) is **infeasible**, if Y is known but k is **not** known

The "trap-door" is the private key — knowing it makes inversion easy; without it, inversion is infeasible.

---

## Q4. Explain the Diffie-Hellman Key Exchange algorithm.

### Introduction
- The **first** public-key-type scheme, proposed by **Diffie & Hellman in 1976**.
- It is **not** an encryption/decryption algorithm — it is **limited to the secure exchange of a secret key** between two parties.
- The exchanged key is then used for subsequent **symmetric** encryption of messages.
- It is based on the difficulty of computing **discrete logarithms**.

### Global Public Elements
- **q** — a prime number
- **α** — an integer that is a **primitive root** of q (α < q)

(A primitive root α of a prime q is a number whose powers α¹, α², …, α^(q−1) mod q generate all integers from 1 to q − 1.)

### The Algorithm

| Step | Alice (User A) | Bob (User B) |
|------|---------------|--------------|
| Generate private key | Select X_A such that X_A < q | Select X_B such that X_B < q |
| Calculate public key | Y_A = α^(X_A) mod q | Y_B = α^(X_B) mod q |
| Exchange | Sends Y_A to Bob, receives Y_B | Sends Y_B to Alice, receives Y_A |
| Compute shared secret | K = (Y_B)^(X_A) mod q | K = (Y_A)^(X_B) mod q |

**Why both compute the same K:**
```
K = (Y_B)^(X_A) mod q = (α^(X_B))^(X_A) mod q = α^(X_A · X_B) mod q
K = (Y_A)^(X_B) mod q = (α^(X_A))^(X_B) mod q = α^(X_A · X_B) mod q
```
Both arrive at the same value, so the shared secret key is established without ever transmitting it.

### Worked Example
Let q = 353 and α = 3 (a primitive root of 353).
- Private keys: X_A = 97, X_B = 233
- Y_A = 3⁹⁷ mod 353 = **40**
- Y_B = 3²³³ mod 353 = **248**
- Shared key:
  - Alice: K = 248⁹⁷ mod 353 = **160**
  - Bob: K = 40²³³ mod 353 = **160** ✓

An attacker knows q = 353, α = 3, Y_A = 40, Y_B = 248 — but **not** the private keys X_A, X_B, and cannot easily compute K because the discrete logarithm problem is hard.

### Man-in-the-Middle (MITM) Attack
Diffie-Hellman is **vulnerable to a man-in-the-middle attack** because it does not authenticate the participants.

1. Darth generates two private keys X_D1, X_D2 and computes public keys Y_D1, Y_D2.
2. Alice sends Y_A → Darth intercepts it, sends Y_D1 to Bob, and computes K2 = (Y_A)^(X_D2) mod q.
3. Bob receives Y_D1, computes K1 = (Y_D1)^(X_B) mod q.
4. Bob sends Y_B → Darth intercepts it, sends Y_D2 to Alice, computes K1 = (Y_B)^(X_D1) mod q.
5. Alice receives Y_D2, computes K2 = (Y_D2)^(X_A) mod q.

**Result:** Alice and Darth share K2; Bob and Darth share K1. Darth can decrypt, read, and even modify all messages between Alice and Bob, while both believe they share a key with each other.

**Countermeasure:** Authenticate public keys using digital signatures or certificates (authenticated Diffie-Hellman).

---

## Q5. Explain the Principles / Model of a Public-Key Cryptosystem.

### Motivation
Public-key cryptography was developed to address **two key problems** of symmetric cryptography:
1. **Key distribution** — how to communicate securely without trusting a Key Distribution Center (KDC) with your key.
2. **Digital signatures** — how to verify that a message comes intact from the claimed sender.

### Characteristics of Asymmetric Algorithms
- Rely on **one key for encryption** and a **different but related key for decryption**.
- It is **computationally infeasible** to determine the decryption key given only the algorithm and the encryption key.
- For algorithms like RSA, **either** of the two related keys can be used for encryption, with the other used for decryption.

### Five Ingredients of a Public-Key Encryption Scheme
1. **Plaintext** — the readable message/data fed as input.
2. **Encryption algorithm** — performs transformations on the plaintext.
3. **Public and private keys** — a selected pair; if one encrypts, the other decrypts.
4. **Ciphertext** — the scrambled output (depends on plaintext + key).
5. **Decryption algorithm** — accepts ciphertext + matching key and recovers the plaintext.

### Essential Operating Steps
1. Each user generates a pair of keys for encryption and decryption.
2. Each user places one key in a **public register** (the public key) and keeps the companion key **private**. Each user maintains a collection of public keys obtained from others.
3. If Bob wishes to send a confidential message to Alice, he encrypts it using **Alice's public key**.
4. When Alice receives it, she decrypts it using **her private key**. No one else can decrypt it because only Alice knows her private key.

### Public-Key Certificate
A digital document, issued and digitally signed by the private key of a **Certification Authority (CA)**, that binds the **name of a subscriber** to a **public key**. It indicates that the subscriber identified in the certificate has sole control of and access to the corresponding private key.

*(See Q10 for the confidentiality, authentication, and combined diagrams.)*

---

## Q6. Demonstrate the Blum Blum Shub (BBS) Generator with an example.

### Introduction
Blum Blum Shub is a **Cryptographically Secure Pseudo-Random Number Generator (CSPRNG)**. It produces a sequence of bits and is considered secure because predicting the next bit is as hard as factoring a large number.

### Setup / Algorithm
1. **Selection of primes:** choose two large prime numbers p and q such that:
   ```
   p ≡ q ≡ 3 (mod 4)
   ```
   (i.e. both leave remainder 3 when divided by 4)
2. Compute **n = p × q**.
3. Choose a seed **s** that is **relatively prime to n** (gcd(s, n) = 1), so neither p nor q is a factor of s.
4. Compute the initial value: **X₀ = s² mod n**
5. For i = 1 to ∞:
   ```
   Xᵢ = (Xᵢ₋₁)² mod n
   Bᵢ = Xᵢ mod 2   (least significant bit)
   ```

### Worked Example (p = 7, q = 11)

> Both check: 7 mod 4 = 3 ✓ and 11 mod 4 = 3 ✓

1. p = 7, q = 11
2. n = 7 × 11 = **77**
3. Choose seed s = 12 (relatively prime to 77)
4. X₀ = 12² mod 77 = 144 mod 77 = **67**

Now generate the sequence:

| i | Xᵢ = (Xᵢ₋₁)² mod 77 | Bᵢ = Xᵢ mod 2 |
|---|---------------------|----------------|
| 0 | 67 | — |
| 1 | 67² mod 77 = 4489 mod 77 = 23 | 23 mod 2 = **1** |
| 2 | 23² mod 77 = 529 mod 77 = 67 | 67 mod 2 = **1** |
| ... | (sequence continues) | ... |

So the output bit stream begins **1, 1, …**

### Standard Textbook Example (n = 192649, seed s = 100929)
The generator yields X₀ = 20749, then the sequence X₁ = 143135 (B₁ = 1), X₂ = 177671 (B₂ = 1), X₃ = 97048 (B₃ = 0), and so on — producing the bit stream 1 1 0 0 1 1 1 0 0 0 …

### Why it is secure
The security depends on the difficulty of factoring n. As long as n cannot be factored, an observer cannot predict the next bit better than guessing, making BBS suitable for cryptographic use.

---

## Q7. Explain ECC Diffie-Hellman Key Exchange, Encryption and Decryption.

### Introduction to ECC
- **Elliptic Curve Cryptography** is a public-key cryptosystem that uses elliptic curves.
- It provides **equal security with a much smaller key size** compared to non-ECC algorithms (e.g. RSA), giving a computational and storage advantage.
- An elliptic curve is defined by the equation:
  ```
  y² = x³ + ax + b
  ```

### Global Public Elements
- **E_q(a, b)** — an elliptic curve with parameters a, b, and q, where q is a prime or an integer of the form 2ᵐ.
- **G** — a base point on the curve whose order is a large value n.

### ECC Diffie-Hellman Key Exchange

| Step | User A | User B |
|------|--------|--------|
| Select private key | n_A < n | n_B < n |
| Calculate public key | P_A = n_A × G | P_B = n_B × G |
| Compute shared secret | K = n_A × P_B | K = n_B × P_A |

**Both produce the same key** because:
```
n_A × P_B = n_A × (n_B × G) = n_B × (n_A × G) = n_B × P_A
```

### ECC Encryption / Decryption
Let the message be **m**.

1. **Encode** the message as a point **P_m** (an (x, y) point) on the elliptic curve.
2. **Encryption:** A chooses a random positive integer **k** and produces the ciphertext as a **pair of points**:
   ```
   C_m = { kG, P_m + k·P_B }
   ```
   where P_B is B's public key.
3. **Decryption:** B multiplies the first point by B's private key n_B and subtracts the result from the second point:
   ```
   P_m + k·P_B − n_B(kG)
       = P_m + k(n_B·G) − n_B(kG)
       = P_m
   ```
   recovering the original message point P_m.

### Security of ECC
- Security depends on the difficulty of determining **k** given kG and G — the **Elliptic Curve Logarithm Problem**.
- The fastest known attack is the **Pollard rho method**.
- ECC achieves the same security as RSA with **considerably smaller keys** — e.g. a 256-bit ECC key is comparable to a 3072-bit RSA key.

---

## Q8. Find the shared secret key using Diffie-Hellman (Numerical Problems).

### Problem 1
Users A and B use Diffie-Hellman with common prime **q = 71** and primitive root **α = 7**.

**(a) A's private key X_A = 5 → find public key Y_A:**
```
Y_A = 7^5 mod 71 = 16807 mod 71 = 51
```

**(b) B's private key X_B = 12 → find public key Y_B:**
```
Y_B = 7^12 mod 71 = 4
```

**(c) Shared secret key:**
```
K = (Y_B)^(X_A) mod 71 = 4^5 mod 71 = 1024 mod 71 = 30
(Check: K = (Y_A)^(X_B) mod 71 = 51^12 mod 71 = 30 ✓)
```
**Shared key K = 30**

### Problem 2
Diffie-Hellman with common prime **q = 11** and primitive root **α = 2**.

**(a) A's public key Y_A = 9 → find A's private key X_A:**
Solve 2^(X_A) mod 11 = 9.
```
2^1=2, 2^2=4, 2^3=8, 2^4=5, 2^5=10, 2^6=9  →  X_A = 6
```
**X_A = 6**

**(b) B's public key Y_B = 3 → find shared secret key K:**
```
K = (Y_B)^(X_A) mod 11 = 3^6 mod 11 = 729 mod 11 = 3
```
**Shared key K = 3**

### Simple Example (q = 7, α = 5)
- X_A = 3 → Y_A = 5³ mod 7 = 125 mod 7 = **6**
- X_B = 4 → Y_B = 5⁴ mod 7 = 625 mod 7 = **2**
- K (by A) = 2³ mod 7 = 8 mod 7 = **1**
- K (by B) = 6⁴ mod 7 = 1296 mod 7 = **1** ✓

---

## Q9. Find the random number using Linear Congruential Generator (LCG) parameters (a, c, m, X₀).

### Linear Congruential Generator
An LCG produces a sequence of pseudorandom numbers using four parameters:

| Parameter | Meaning | Constraint |
|-----------|---------|------------|
| m | the modulus | m > 0 |
| a | the multiplier | 0 < a < m |
| c | the increment | 0 ≤ c < m |
| X₀ | the seed (starting value) | 0 ≤ X₀ < m |

The sequence {Xₙ} is generated by the recurrence:
```
X_(n+1) = (a·Xₙ + c) mod m
```
Each Xₙ lies in the range 0 ≤ Xₙ < m.

### Three Tests for a Good Random Number Generator
- **T1 — Full period:** the function should be a full-period generating function (generate all numbers 0 through m − 1 before repeating).
- **T2 — Randomness:** the generated sequence should appear random.
- **T3 — Efficiency:** the function should implement efficiently with 32-bit arithmetic.

A convenient prime value for 32-bit arithmetic is m = 2³¹ − 1, giving X_(n+1) = (a·Xₙ) mod (2³¹ − 1).

### Worked Example
Let a = 7, c = 0, m = 32, X₀ = 1:
```
X₁ = (7×1 + 0) mod 32 = 7
X₂ = (7×7) mod 32 = 49 mod 32 = 17
X₃ = (7×17) mod 32 = 119 mod 32 = 23
X₄ = (7×23) mod 32 = 161 mod 32 = 1   ← repeats (period = 4)
```
Sequence: 1, 7, 17, 23, 1, … (short period — not full-period).

### Example with c ≠ 0
Let a = 5, c = 3, m = 16, X₀ = 7:
```
X₁ = (5×7 + 3) mod 16 = 38 mod 16 = 6
X₂ = (5×6 + 3) mod 16 = 33 mod 16 = 1
X₃ = (5×1 + 3) mod 16 = 8
X₄ = (5×8 + 3) mod 16 = 43 mod 16 = 11
```
Sequence: 7, 6, 1, 8, 11, …

> **Exam tip:** In the actual exam, substitute the (a, c, m, X₀) values given in the question into `X_(n+1) = (a·Xₙ + c) mod m` and iterate for the requested number of terms.

---

## Q10. Explain the Public-Key Cryptosystem for Secrecy and Authentication.

A public-key cryptosystem can be operated in **three modes** depending on which keys are used.

### (a) Confidentiality (Secrecy)
**Goal:** Only the intended receiver can read the message.

- Source A encrypts message X with **B's public key (PUᵦ)**:
  ```
  Y = E(PUᵦ, X)
  ```
- Destination B decrypts with **B's private key (PRᵦ)**:
  ```
  X = D(PRᵦ, Y)
  ```
- **Provides:** Confidentiality only. Since only B has PRᵦ, only B can decrypt. But **no authentication** — anyone could have used B's public key, so B cannot be sure who sent it.

### (b) Authentication
**Goal:** Prove the message came from the claimed sender.

- Source A encrypts message X with **A's own private key (PRₐ)**:
  ```
  Y = E(PRₐ, X)
  ```
- Destination B decrypts with **A's public key (PUₐ)**:
  ```
  X = D(PUₐ, Y)
  ```
- **Provides:** Authentication and a form of digital signature. Since only A has PRₐ, successful decryption with PUₐ proves A sent it. But **no confidentiality** — anyone with A's public key can read it.

### (c) Authentication AND Secrecy (Both)
**Goal:** Confidentiality + authentication together. Uses **double encryption**.

- At source A:
  ```
  Z = E(PUᵦ, E(PRₐ, X))
  ```
  (first sign with A's private key, then encrypt with B's public key)
- At destination B:
  ```
  X = D(PUₐ, D(PRᵦ, Z))
  ```
  (first decrypt with B's private key, then verify with A's public key)
- **Provides:** Both confidentiality (only B can read) **and** authentication (B knows A sent it). The cost is **four public-key operations** per message.

### Summary Table

| Mode | Encrypt with | Decrypt with | Provides |
|------|-------------|-------------|----------|
| Confidentiality | Receiver's public key (PUᵦ) | Receiver's private key (PRᵦ) | Secrecy |
| Authentication | Sender's private key (PRₐ) | Sender's public key (PUₐ) | Authentication / signature |
| Both | PRₐ then PUᵦ | PRᵦ then PUₐ | Secrecy + Authentication |

---

## Q11. Discuss the Abelian Group for Elliptic Curve Arithmetic.

### What is a Group?
A **group** {G, ·} is a set of elements with a binary operation "·" that associates an ordered pair (a, b) to an element (a · b) in G, satisfying:
- **(A1) Closure:** if a, b ∈ G, then a · b ∈ G.
- **(A2) Associativity:** a · (b · c) = (a · b) · c for all a, b, c ∈ G.
- **(A3) Identity element:** there exists e ∈ G such that a · e = e · a = a.
- **(A4) Inverse element:** for each a ∈ G, there exists a⁻¹ ∈ G such that a · a⁻¹ = a⁻¹ · a = e.

### Abelian Group
A group is **Abelian (commutative)** if it additionally satisfies:
- **(A5) Commutativity:** a · b = b · a for all a, b ∈ G.

### Elliptic Curves Form an Abelian Group
The set of points on an elliptic curve, together with a special **point at infinity O**, forms an **Abelian group under the "point addition" operation**. The geometric/algebraic rules are:

1. **Identity (point at infinity O):** O serves as the additive identity. For any point P, **P + O = P**. Thus O = −O.

2. **Negative (inverse) of a point:** The negative of P = (x, y) is its reflection across the x-axis: **−P = (x, −y)**. A point and its negative add to O: **P + (−P) = O**.

3. **Point addition (P + Q):** To add two distinct points P and Q, draw a straight line through them. It intersects the curve at a third point. The **reflection** of that third point across the x-axis is **P + Q = R**.

4. **Point doubling (P + P = 2P):** When P = Q, draw the **tangent** to the curve at P. It meets the curve at one other point; its reflection across the x-axis gives **2P**.

### Verifying the Abelian Group Properties
- **Closure:** the sum of two points on the curve is another point on the curve. ✓
- **Associativity:** (P + Q) + R = P + (Q + R). ✓
- **Identity:** the point at infinity O. ✓
- **Inverse:** every point P has a negative −P with P + (−P) = O. ✓
- **Commutativity:** P + Q = Q + P (the line through P and Q is the same line through Q and P). ✓

Because all five properties hold, the points on an elliptic curve form an **Abelian group**, which is the algebraic foundation that makes elliptic curve cryptography (key exchange, encryption, signatures) possible.

### Algebraic Formulas (for E over real numbers)
For P = (x_P, y_P) and Q = (x_Q, y_Q), the sum R = (x_R, y_R) is:
```
x_R = λ² − x_P − x_Q
y_R = λ(x_P − x_R) − y_P

where the slope λ is:
  λ = (y_Q − y_P)/(x_Q − x_P)        if P ≠ Q   (addition)
  λ = (3·x_P² + a)/(2·y_P)           if P = Q   (doubling)
```

---

## Quick Revision — High-Priority Topics
Based on past papers, focus most on these:

1. **RSA Algorithm** (appears in all 3 papers) — Q1
2. **Diffie-Hellman Key Exchange** (appears in all 3 papers) — Q4, Q8
3. **Public-Key Cryptosystem Model / Principles** (appears in all 3 papers) — Q5, Q10
4. **Blum Blum Shub Generator** (appears in 2 papers) — Q6

> **Practice well:** the numerical problems (RSA encryption/decryption, Diffie-Hellman shared key, BBS bit sequence, LCG sequence) are the most commonly tested and the easiest to score full marks on if you show every step.
