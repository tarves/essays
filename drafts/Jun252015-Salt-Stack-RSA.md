##Why the RSA Public Key Should Not Equal 1



Not that long ago, Salt Stack, open-source software that is used to manage and configure cloud application software, was found to have been improperly using public-key RSA cryptography.  They had set their public key to the number 1 for performance reasons.  This, it turned out, was a serious security vulnerability and I wanted to discuss why that is.  You can see the actual git commit that changed the code [here]().  The public key was changed from 1 to 65537.  65537 was not selected randomly, there is a good reason it was chosen.

One of the first things needed is a simple explanation of how RSA public key cryptography works and to get this, we need to review some of the mathematics behind it.

The first thing needed is a review of Euler's Theorem.  It states that
	If n and a are coprime, positive integers then
	a<sup>&phi(n)</sup> &equiv 1 (mod n).
	where &phi is Euler's Totient Function.

Euler's Totient Function, &phi(n), is simply the quantity of positive integers less than or equal to n that are relatively prime to n.

####The RSA Function

The actual RSA cryptosystem functions as follows.

1.  Choose two prime numbers, p and q, where pq = N and where N is the modulus. We want p,q roughly the size of the square root of the number of bits of N.

2.  Compute the totient function, &phi(N) = &phi(p)&phi(q) = (p-1)(q-1) = N-p-q+1,  notice that &phi(N) is close to N.

3.  Choose a public exponent, e such that 1 < e < &phi(N).  Notice that 1 is out of bounds of the acceptable range of e.

4.  Compute a private exponent, d, such that d &equiv e<sup>-1</sup) (mod &phi(N)) or d*e &equiv 1 (mod &phi(N)). 

5.  Let the public key, pk = (N, e) and the private or secret key, sk = (N, d).

The RSA function will be defined as follows.

The encryption function, F(pk, e) maps the integers modulo N to itself, the integers modulo N.  We shall call it RSA(x) = x<sup>e</sup>.

The decryption function F<sup>-1</sup>(sk, d) = y<sup>d</sup>.

Thus, y<sup>d</sup> = RSA<sup>d</sup> = x<sup>ed</sup> = x<sup>k&phi(N)+1 = (x<sup>&phi(N)</sup>k * x = x.

####So What Happens When e = 1?

Well, two things happen.

First of all, from number 4 above, where e = 1, d &equiv 1 (mod &phi(N)) so the private key can be computed from this.

Second, the encryption function, RSA(x) is now x<sup>1</sup> (mod N) so x (mod N).  So, if x is less than N, then no encryption occurred. 
  
