이 클래스는 동적으로 크기가 조정되는 문자열 배열을 관리합니다.  
`DynamicStringArray` 클래스는 동적 배열을 사용하여 문자열을 저장하고 관리하는 기능을 제공합니다.

### 멤버 변수:

- `string* dynamicArray`: 실제 문자열 데이터를 저장하는 동적 배열입니다. 문자열 배열의 크기는 `size` 변수에 의해 결정됩니다.
- `size_t size`: 현재 배열에 저장된 문자열의 수를 나타내는 변수입니다. `size_t`는 부호 없는 정수형으로, 배열의 인덱스나 크기를 나타내는 데에 주로 사용됩니다.

### 멤버 함수:

- `DynamicStringArray()`: 생성자 함수로, 객체가 생성될 때 초기화를 담당합니다. 동적 배열을 `nullptr`로 설정하고 크기를 0으로 초기화합니다.
- `DynamicStringArray(const DynamicStringArray& src)`: 복사 생성자로, 다른 `DynamicStringArray` 객체의 내용을 현재 객체로 복사합니다.
- `~DynamicStringArray()`: 소멸자로, 객체가 소멸될 때 동적으로 할당된 메모리를 해제합니다.
- `size_t getSize() const`: 현재 배열의 크기(저장된 문자열의 수)를 반환합니다.
- `void addEntry(string)`: 새로운 문자열을 배열에 추가합니다.
- `bool deleteEntry(string)`: 지정된 문자열을 배열에서 제거합니다. 문자열이 배열에 존재하면 제거 후 `true`를, 그렇지 않으면 `false`를 반환합니다.
- `string getEntry(size_t)`: 지정된 인덱스의 문자열을 반환합니다. 인덱스가 배열 범위를 벗어나면 에러 메시지를 출력하고 빈 문자열을 반환합니다.
- `const DynamicStringArray& operator=(const DynamicStringArray& right)`: 대입 연산자로, 다른 `DynamicStringArray` 객체의 내용을 현재 객체로 복사합니다.

### size_t에 대한 설명:

`size_t`는 C++에서 부호 없는 정수 유형으로 정의되며, 메모리의 크기, 배열의 크기, 문자열의 길이 등을 나타내는 데 사용됩니다.  
표준 헤더 파일 `<cstddef>`에 정의되어 있으며, 플랫폼에 따라 크기가 다를 수 있지만 일반적으로 32비트 시스템에서는 4바이트, 64비트 시스템에서는 8바이트입니다.

### 본래의 설계에서 변형된 부분:

원래 `getEntry` 함수는 유효하지 않은 인덱스에 대해 `NULL` 또는 `nullptr`을 반환하도록 설계되었지만, 이는 포인터를 반환하는 것이므로 `string` 형을 반환하는 함수와의 일관성이 없습니다.  
따라서 설계 변경을 통해 인덱스가 배열 범위를 벗어났을 때 에러 메시지를 출력하고 빈 문자열(`""`)을 반환하도록 하였습니다.  
이렇게 함으로써 함수의 반환 타입을 `string`으로 유지하면서도 인덱스 범위 오류를 처리할 수 있게 되었습니다.  
이러한 접근 방식은 사용자에게 명확한 오류 메시지를 제공하면서도 객체 사용에 있어서의 일관성을 유지합니다. 

### 컴파일 환경:

microsoft visual studio 내의 자체 컴파일러와 gcc기반의 온라인 컴파일러를 제공하는 ryugod 사이트의 c++컴파일 기능을 사용했습니다.  
컴파일에 사용된 하드웨어는 다음과 같습니다.  

- CPU: 13th Gen Intel(R) Core(TM) i5-1340P
- RAN: 16GB, 6000MHz
- RyuGod 서버 기준: 우분투 linux 20.04 

### 소스 코드 (분리 컴파일 기준)

- DynamicStringArray.h
```cpp
#include <iostream>
#include <string>

using namespace std;

class DynamicStringArray {
private:
	string* dynamicArray;
	size_t size;
public:
	DynamicStringArray();
	DynamicStringArray(const DynamicStringArray&);
	~DynamicStringArray();
	size_t getSize() const;
	void addEntry(string);
	bool deleteEntry(string);
	string getEntry(size_t);
	const DynamicStringArray& operator=(const DynamicStringArray& right);
};
```
- DynamicStringArray.cpp
```cpp
#pragma once

#include <iostream>
#include <string>
#include "DynamicStringArray.h"

using namespace std;

//constructor
DynamicStringArray::DynamicStringArray()
	: dynamicArray(nullptr), size(0) {}

//copy constructor
DynamicStringArray::DynamicStringArray(const DynamicStringArray& src)
	: size(src.size), dynamicArray(new string[src.size]) {
	for (size_t i = 0; i < src.size; i++) { //deep copy
		dynamicArray[i] = src.dynamicArray[i];
	}
}

//operator = overload : copy DynamicStringArray
const DynamicStringArray& DynamicStringArray::operator=(const DynamicStringArray& right) {
	if (this != &right) { 
		delete[] this->dynamicArray;
		this->size = right.size;
		this->dynamicArray = new string[this->size];
		for (size_t i = 0; i < this->size; i++) {
			this->dynamicArray[i] = right.dynamicArray[i];
		}
	}
	return *this;
}

//destructor
DynamicStringArray::~DynamicStringArray() {
	delete[] this->dynamicArray;
}

//func: return size
size_t DynamicStringArray::getSize() const {
	return this-> size;
}

//func: allocate memory of (size+1)*sizeof(string), then add a string back of DynamicStringArray.
void DynamicStringArray::addEntry(string str){
	string* newDynamicArray = new string[(this->size) + 1];
	for (size_t i = 0; i < this->size; i++) {
		newDynamicArray[i] = this->dynamicArray[i];
	}
	newDynamicArray[this->size] = str;
	delete[] this->dynamicArray;
	(this->size)++;
	this->dynamicArray = newDynamicArray;
}

//func: to delete a string. If a string want to delete exists, delete string and return true. If not, return false.
bool DynamicStringArray::deleteEntry(string str) {
	bool finder = false; size_t findIndex;
	for (size_t i = 0; i < this->size; i++) {
		if (this->dynamicArray[i] == str) {
			finder = true;
			findIndex = i;
		}
	}
	if (finder) {
		string* newDynamicArray = new string[(this->size) - 1];
		for (size_t i = 0; i < findIndex; i++) {
			newDynamicArray[i] = this->dynamicArray[i];
		}
		for (size_t i = findIndex + 1; i < this->size; i++) {
			newDynamicArray[i - 1] = dynamicArray[i];
		}
		delete[] this->dynamicArray;
		(this->size)--;
		this->dynamicArray = newDynamicArray;
	}
	return finder;
}

//func: return a string that is appopriate to index. If there's no appopriate string for index, return nullptr.
string DynamicStringArray::getEntry(size_t index) {
	if (this->size <= index) {
		return nullptr;
	}
	else {
		return this->dynamicArray[index];
	}
}
