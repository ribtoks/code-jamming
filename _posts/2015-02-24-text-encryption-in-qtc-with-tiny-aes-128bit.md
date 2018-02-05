---
title: Text encryption in Qt/C++ with tiny AES 128bit
date: 2015-02-24T15:12:39+00:00
author: "Taras Kushnir"
layout: post
permalink: /text-encryption-in-qtc-with-tiny-aes-128bit/
categories:
  - C++
  - Programming
  - Qt
tags:
  - aes
  - c++
  - data
  - encryption
  - qt
  - text
---
Have you ever needed a small, really small encryption in your C++ project for some piece of text? Say, credentials, login details or any other sensitive data? Of course, the best way is to keep just hash of salted password, but... What if you just **need** to do it and the size is so much critical for you?

There're <a href="https://www.openssl.org/" target="_blank" rel="noopener">openSSL</a> library and <a href="http://www.cryptopp.com/" target="_blank" rel="noopener">Crypto++</a> library which are monsters with tons of encryption algorithms, used in a number of solid projects etc. But.. they are big! I don't want 30Mb library in my tiny project, which weights 10 Mb with high-resolution icons for OS X which weight by itself 5Mb. So I don't want to sacrifice the size but still need encryption. Meet <a href="https://github.com/kokke/tiny-AES128-C" target="_blank" rel="noopener">tiny-AES.</a> It's really small AES 128-bit library which does encryption in <a href="https://en.wikipedia.org/wiki/Block_cipher_modes_of_operation" target="_blank" rel="noopener">CBC and ECB modes</a>. It really contains everything you needed just to encrypt and decrypt your sensitive data and forget about it.

You can find example under the hood.

<!--more-->

Tiny-AES is not super-strong, it implements AES, it's build for ARMs.. but it works! It's easy to adopt to any C++ project. Here's how I implemented encryption in my small Qt C++ project.

Key for AES-128 should be 128 bit length, so I used MD5 hashing to get exactly 128 bit buffer for AES key. Cypher-text should be 16 bit aligned, so I use my own inlined alignment function (which could be macro etc.). Also I used _utf8()_ method of QString to get pointer to underlying _ushort*_ buffer and to encode directly it.

```cpp
#ifndef AESQT_H
#define AESQT_H

# tons of other includes
#include "../tiny-aes/aes.h"

namespace Encryption {

    const uint8_t iv[] = { 0xf0, 0xe1, 0xd2, 0xc3, 0xb4, 0xa5, 0x96, 
              0x87, 0x78, 0x69, 0x5a, 0x4b, 0x3c, 0x2d, 0x5e, 0xaf };

    inline int getAlignedSize(int currSize, int alignment) {
        int padding = (alignment - currSize % alignment) % alignment;
        return currSize + padding;
    }

    QString encodeText(const QString &rawText, const QString &key) {
        QCryptographicHash hash(QCryptographicHash::Md5);
        hash.addData(key.toUtf8());
        QByteArray keyData = hash.result();

        const ushort *rawData = rawText.utf16();
        void *rawDataVoid = (void*)rawData;
        const char *rawDataChar = static_cast(rawDataVoid);
        QByteArray inputData;
        // ushort is 2*uint8_t + 1 byte for '\0'
        inputData.append(rawDataChar, rawText.size() * 2 + 1);

        const int length = inputData.size();
        int encryptionLength = getAlignedSize(length, 16);

        QByteArray encodingBuffer(encryptionLength, 0);
        inputData.resize(encryptionLength);

        AES128_CBC_encrypt_buffer((uint8_t*)encodingBuffer.data(), (uint8_t*)inputData.data(),
           encryptionLength, (const uint8_t*)keyData.data(), iv);

        QByteArray data(encodingBuffer.data(), encryptionLength);
        QString hex = QString::fromLatin1(data.toHex());
        return hex;
    }

    QString decodeText(const QString &hexEncodedText, const QString &key) {
        QCryptographicHash hash(QCryptographicHash::Md5);
        hash.addData(key.toUtf8());
        QByteArray keyData = hash.result();

        const int length = hexEncodedText.size();
        int encryptionLength = getAlignedSize(length, 16);

        QByteArray encodingBuffer(encryptionLength, 0);

        QByteArray encodedText = QByteArray::fromHex(hexEncodedText.toLatin1());
        encodedText.resize(encryptionLength);

        AES128_CBC_decrypt_buffer((uint8_t*)encodingBuffer.data(), (uint8_t*)encodedText.data(), 
          encryptionLength, (const uint8_t*)keyData.data(), iv);

        encodingBuffer.append("\0\0");
        void *data = encodingBuffer.data();
        const ushort *decodedData = static_cast(data);
        QString result = QString::fromUtf16(decodedData);
        return result;
    }
}

#endif // AESQT_H```

I wrote <a href="https://github.com/Ribtoks/xpiks/blob/master/src/xpiks-tests/encryption_tests.cpp" target="_blank" rel="noopener" class="broken_link">a bunch of tests</a> against that functions so the solution is proven to be working. So if you're looking for really tiny encryption or AES implementation, use <a href="https://github.com/kokke/tiny-AES128-C" target="_blank" rel="noopener">tiny-AES-128</a>!