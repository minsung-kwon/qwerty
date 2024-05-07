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

### 소스 코드

- DynamicStringArray.cpp
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
	const DynamicStringArray& operator=(const DynamicStringArray& right);
	~DynamicStringArray();
	size_t getSize() const;
	void addEntry(string);
	bool deleteEntry(string);
	string getEntry(size_t);
};

DynamicStringArray::DynamicStringArray()
	: dynamicArray(nullptr), size(0) {}

DynamicStringArray::DynamicStringArray(const DynamicStringArray& src)
	: size(src.size), dynamicArray(new string[src.size]) {
	for (size_t i = 0; i < src.size; i++) {
		dynamicArray[i] = src.dynamicArray[i];
	}
}

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

DynamicStringArray::~DynamicStringArray() {
	cout << "Destructor Called\n";
	delete[] this->dynamicArray;
}

size_t DynamicStringArray::getSize() const {
	return this-> size;
}

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

string DynamicStringArray::getEntry(size_t index) {
	if (this->size <= index) {
		return nullptr;
	}
	else {
		return this->dynamicArray[index];
	}
}

int main() {
	DynamicStringArray strarr;
	string str;
	str = "qwerty";
	cout << "getSize test\n" << strarr.getSize() << "\n";

	cout << '\n';

	strarr.addEntry(str);
	cout << "addEntry-size test\n" << strarr.getSize() << "\n";
	cout << "addEntry-index test: index[0]\n" << strarr.getEntry(0) << "\n";

	cout << '\n';

	DynamicStringArray copy_testcase(strarr);
	cout << "size test for copy constructor\n" << copy_testcase.getSize() << "\n";
	cout << "index test for copy constructor\n" << copy_testcase.getEntry(0) << "\n";
	DynamicStringArray operator_testcase;
	operator_testcase = strarr;
	cout << "size test for operator \'=\'\n" << operator_testcase.getSize() << "\n";
	cout << "index test for operator \'=\'\n" << operator_testcase.getEntry(0) << "\n";

	cout << '\n';

	if (!strarr.deleteEntry("abcdef")) {
		cout << "deleteEntry test\n";
	}
	if (strarr.deleteEntry(str)) {
		cout << "deleteEntry test\n";
	}

	cout << '\n';

	DynamicStringArray* dynamic_alloc_testcase = new DynamicStringArray[2];
	dynamic_alloc_testcase[0].addEntry(str);
	dynamic_alloc_testcase[1].addEntry("index 0");
	dynamic_alloc_testcase[1].addEntry("index 1");
	cout << "size test for dynamic alloc 1\n" << dynamic_alloc_testcase[0].getEntry(0) << "\n";
	cout << "size test for dynamic alloc 2\n" << dynamic_alloc_testcase[1].getEntry(0) << "\n";
	cout << "size test for dynamic alloc 3\n" << dynamic_alloc_testcase[1].getEntry(1) << "\n";

	cout << '\n';

	cout << "delete destructor test, destructor will be called 2 times\n" << "if destructor called, print message \"Destructor Called\"\n";
	delete[] dynamic_alloc_testcase;

	cout << '\n';

	cout << "error code (invalid index) test, index value is 0, DynamicStringArray size is empty now\nprogram will end" << strarr.getEntry(0) << "\n";
	return 0;
}
```

>코드 테스트
1. 생성자와 getSize함수를 테스트한다.
2. addEntry함수에서의 사이즈 증가와 문자열 추가를 테스트한다.  이때 getEntry함수 또한 함께 테스트된다.  정상적으로 동작한다면 객체의 size와 "qwerty"가 출력된다.
3. 복사 생성자와 대입 연산자를 통한 객체 대입을 테스트한다.  정상적으로 동작한다면 복사 생성자와 대입 연산자를 통한 테스트 모두 1, "qwerty"가 출력된다.
4. deleteEntry함수를 테스트한다.  "abcdef"를 "qwerty"하나의 문자열만이 들어가 있는 객체 strarr에 있는지 확인한다.  없다면 false가 반환되어 조건문에 따라 "deleteEntry test"가 출력된다.  "qwerty"를 "qwerty"하나의 문자열만이 들어가 있는 객체 strarr에 있는지 확인한다.  있다면 false가 반환되어 조건문에 따라 "deleteEntry test"가 출력된다.  정상적으로 작동한다면 "deleteEntry test"가 2번 출력된다.
5. 동적할당을 테스트한다.  크기가 2인 DynamicStringArray배열을 만든다.  동적할당한 배열의 0번 인덱스에 "qwerty"문자열을 addEntry한다.  동적할당한 배열의 1번 인덱스에 "index 0"문자열을 addEntry하고, 1번 인덱스에 "index 1"문자열을 addEntry힌디.  그리고 이들을 모두 getEntry로 출력한다.  정상적으로 동작한다면 각 줄별로 "qwerty", "index 0", "index 1"이 출력된다.
6. 소멸자를 테스트한다. 앞쪽에서 동적할당 되었던 객체들을 소멸시킨다.  소멸자가 호출될 떄마다 "Destructor Called"가 출력된다.  배열의 크기를 2로 동적할당했으므로, 정상적으로 동작한다면 "Destructor Called"가 2번 출력된다.
7. getEntry를 테스트한다.  올바르게 작동되는 경우는 2번에서 테스트되었다.  이 테스트는 입력된 인덱스가 범위를 벗어났을 경우를 테스트한다.  올마르게 작동된다면 nullptr가 반환되어 에러로 인해 프로그램이 종료된다.
