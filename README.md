CStringSpliter
==============

C++ class to split a string into pieces by given delimiters.


CStringSpliter.h
----------------

```c++
/* CStringSpliter.h */

#pragma once
#ifndef CSTRINGSPLITER_H
#define CSTRINGSPLITER_H

class CStringSpliter {
public:
  enum {EMPTIES_OK = 0, NO_EMPTIES};

public:
	CStringSpliter(const char* str, const char* delimiters, int empties = CStringSpliter::EMPTIES_OK);
	~CStringSpliter();
	const char* Next();

private:
	char*       m_string;
	const char* m_delimiters;
	char*       m_current;
	char*       m_next;
	bool        m_is_ignore_empties;

private:
	char* Sdup(const char* s);
};

#endif 
```

CStringSpliter.cpp
------------------

```c++
/* CStringSpliter.cpp */

#include <stdlib.h>
#include <string.h>
#include "CStringSpliter.h"

CStringSpliter::CStringSpliter(const char* str, const char* delimiters, int empties)
{
  this->m_string            = (str && delimiters) ? this->Sdup(str) : NULL;
	this->m_delimiters        = delimiters;
	this->m_current           = NULL;
	this->m_next              = this->m_string;
	this->m_is_ignore_empties = (empties != CStringSpliter::EMPTIES_OK);
}

CStringSpliter::~CStringSpliter()
{
	free(this->m_string);
	this->m_string = NULL;
}

char* CStringSpliter::Sdup(const char* s)
{
	size_t n = strlen(s) + 1;
	char  *p = (char*)malloc(n);
	return p ? (char*)memcpy(p, s, n) : NULL;
}

const char* CStringSpliter::Next()
{
	if (!this->m_string || !this->m_next) {
		return NULL;
	}
	
	this->m_current = this->m_next;
	this->m_next = strpbrk(this->m_current, this->m_delimiters);
	
	if (this->m_next) {
		*(this->m_next) = '\0';
		this->m_next += 1;
		if (this->m_is_ignore_empties) {
			this->m_next += strspn(this->m_next, this->m_delimiters);
			if (!(*(this->m_current))) {
				return this->Next();
      }
		}
	} else if (this->m_is_ignore_empties && !(*(this->m_current))) {
		return NULL;
	}
	
	return this->m_current;
}
```

Usage
-----

```c++
char *str = "$PTNL,PJK,102823.80,012613,+3541087.713,N,+1878282.023,E,1,16,0.8,EHT+52.668,M*4F";
int n;
const char* sec;
CStringSpliter spliter(str, ",");
for (n=0, sec = spliter.Next(); sec; sec=spliter.Next(), n++) {
	printf("%d: %s\n", n, sec);
}
```

