#!/usr/bin/env python

'''
    File name: rsa.py
    Author: Galaad Couillec
    Date created: 10/02/2016
    Date last modified: 10/03/2016
'''

__author__ = "Galaad Couillec"
__version__ = "1.0.0"
__status__ = "alpha"

from random import randrange, randint
import argparse
import time
from fractions import gcd
from __builtin__ import pow as powmod
import struct
import math
import sys
import os

parser = argparse.ArgumentParser(description='RSA')
parser.add_argument('-b', '--bits', help='Bits', default=1024)
parser.add_argument('-k', '--iteration', help='Iteration', default=25)
parser.add_argument('--generate-random-file', help='Generate random file', action='store_true')
parser.add_argument('--random-file-size', help='Random file size', default=4096)
parser.add_argument('--generate-rsa-keys', help='Generate RSA keys', action='store_true')
parser.add_argument('--encrypt', help='Encrypt', action='store_true')
parser.add_argument('--encrypt-key', help='Encrypt key', default='rsa.public.key')
parser.add_argument('--encrypt-input', help='Encrypt input', default='message')
parser.add_argument('-o', '--output', help='Output', default='output')
args = vars(parser.parse_args())
bits = int(args['bits'])
iteration = int(args['iteration'])
generate_random_file = args['generate_random_file']
random_file_size = int(args['random_file_size'])
generate_rsa_keys = args['generate_rsa_keys']
encrypt = args['encrypt']
encrypt_key = args['encrypt_key']
encrypt_input = args['encrypt_input']
output = args['output']

def get_size (n):
    s = 1
    while n >= 2:
        r = n % 2 
        q = n // 2
        n = q
        s = s + 1
    return s

def gen_rand_int (size):
    i = 1
    res = 1
    while i < size:
        x = randint (0,1)
        res = res * 2 + x
        i = i + 1
    return res

small_primes = [2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31] # etc.

def probably_prime(n, k):
    """Return True if n passes k rounds of the Miller-Rabin primality
    test (and is probably prime). Return False if n is proved to be
    composite.

    """
    if n < 2: return False
    for p in small_primes:
        if n < p * p: return True
        if n % p == 0: return False
    r, s = 0, n - 1
    while s % 2 == 0:
        r += 1
        s //= 2
    for _ in range(k):
        a = randrange(2, n - 1)
        x = pow(a, s, n)
        if x == 1 or x == n - 1:
            continue
        for _ in range(r - 1):
            x = pow(x, 2, n)
            if x == n - 1:
                break
        else:
            return False
    return True

def gen_prime (bits):
    n = gen_rand_int(bits)
    if n % 2 == 0:
        n = n + 1
    while True:
        if probably_prime (n, iteration):
            return n
        else:
            n = n + 2

def get_current_time():
    return int(round(time.time() * 1000))            

def euclide_extended (a,b):
    r = a
    rp = b
    u = 1
    v = 0
    up = 0
    vp = 1
    while rp != 0:
        q = r // rp
        rs = r
        us = u
        vs = v
        r = rp
        u = up
        v = vp
        rp = rs - q * rp
        up = us - q * up
        vp = vs - q * vp
    return (r, u, v)

def write_bigint (f, e):
    r = e % 2**8
    q = e // 2**8
    while r != 0 or q != 0:
        f.write(struct.pack('B',r))
        e = (e - r) / 2**8
        r = e % 2**8
        q = e // 2**8

def read_bigint (f):
    size = os.path.getsize(f.name)
    i = 0
    a = []
    while i < size:
        x = struct.unpack ('B', f.read(1))[0]
        a.append (x)
        i = i + 1
    a.reverse()
    res = 0
    for y in a:
        res = res * 2**8 + y
    return res

def gen_keys ():
    start_time = get_current_time()
    p =  gen_prime (bits)
    after_p = get_current_time()
    t1 = (after_p - start_time) / 1000.0
    print "p = " + str(p)
    print "p generated in " + str(t1) + " s"
    print "p size = " + str(get_size(p))
    q =  gen_prime (bits)
    after_q = get_current_time()
    t2 = (after_q - after_p) / 1000.0
    print "q = " + str(q)
    print "q generated in " + str(t2) + " s"
    print "q size = " + str(get_size(q))

    n = p * q
    print "n = " + str(n)
    print "n size = " + str(get_size(n))

    f = open ("rsa.n", "wb")
    write_bigint(f, n)
    f.close()

    euler = (p-1)*(q-1)
    print "euler = " + str(euler)
    print "euler size = " + str(get_size(euler))

    e = randint (0,euler)
    while True:
        if gcd (e, euler) == 1:
            break
        else:
            e = randint (0,euler)
    print "e = " + str(e)
    print "e size = " + str(get_size(e))

    f = open (output + ".public.key", "wb")
    write_bigint (f, e)
    f.close()

    (r,u,v) = euclide_extended (e, euler)
    while u < 0:
        u = u + euler
    d = u
    print "d = "+ str(d)
    print "d size = " + str(get_size(d))

    f = open (output + ".private.key", "wb")
    write_bigint (f, d)
    f.close()

    x = (d * e) % euler
    print "x = " + str(x)

    message = 123456789
    print "message = " + str(message)
    
    cypher = powmod (message, e, n)
    print "cypher = " + str(cypher)
    
    message_back = powmod (cypher, d, n)
    print "message back = " + str(message_back)

def encrypt ():
    f = open ("rsa.n","rb")
    n = read_bigint (f)
    f.close()
    f = open ("rsa.public.key","rb")
    e = read_bigint (f)
    f.close()
    print e

def gen_random_file (filename, size):
    f = open (filename, "wb")
    n = 0
    while n < size:
        a = randint(0,2**8-1)
        f.write(struct.pack('B',a))
        n = n + 1
    f.close()

if generate_rsa_keys:
    output = "rsa"
    gen_keys()
elif generate_random_file:
    output = "message"
    gen_random_file(output, random_file_size)
elif encrypt:
    output = "cypher"
    encrypt()
