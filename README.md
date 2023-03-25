# RSA-pyton
import math

# 소수 판별 함수
def is_prime(n):
    if n <= 1:
        return False
    elif n <= 3:
        return True
    elif n % 2 == 0 or n % 3 == 0:
        return False
    i = 5
    while i * i <= n:
        if n % i == 0 or n % (i + 2) == 0:
            return False
        i += 6
    return True

# 최대공약수 함수
def gcd(a, b):
    while b != 0:
        temp = a % b
        a = b
        b = temp
    return a

# 확장 유클리드 호제법 함수
def extended_gcd(a, b):
    if a == 0:
        return (b, 0, 1)
    else:
        gcd, x, y = extended_gcd(b % a, a)
        return (gcd, y - (b // a) * x, x)

# 모듈러 역원 함수
def modinv(a, m):
    gcd, x, y = extended_gcd(a, m)
    if gcd != 1:
        raise Exception('modular inverse does not exist')
    else:
        return x % m

# RSA 키 생성 함수
def generate_keypair(p, q):
    if not (is_prime(p) and is_prime(q)):
        raise ValueError('Both numbers must be prime.')
    elif p == q:
        raise ValueError('p and q cannot be equal.')
    # n = pq
    n = p * q
    # φ(n) = (p-1)(q-1)
    phi = (p - 1) * (q - 1)
    # e: 1 < e < φ(n), gcd(e, φ(n)) = 1
    e = 2
    while gcd(e, phi) != 1:
        e += 1
    # d: ed ≡ 1 (mod φ(n))
    d = modinv(e, phi)
    return ((e, n), (d, n))

# RSA 암호화 함수
def encrypt(pk, plaintext):
    # pk는 (e, n) 형태의 튜플
    e, n = pk
    # 각 문자를 숫자로 변환 후 모듈러 연산 적용
    cipher = [pow(ord(char), e, n) for char in plaintext]
    # 각 숫자를 문자로 변환 후 합침
    return ''.join(map(str,cipher))

# RSA 복호화 함수
def decrypt(pk, ciphertext):
    # pk는 (d, n) 형태의 튜플
    d, n = pk
    # 각 숫자를 복호화 후 문자로 변환
    plain = [chr(pow(char, d, n)) for char in ciphertext]
    # 각 문자를 합침
    return ''.join(plain)

# RSA 키 생성 예시
p = 61
q = 53
public, private = generate_keypair(p, q)
print(f"공개키: {public}")
print(f"비밀키: {private}")
