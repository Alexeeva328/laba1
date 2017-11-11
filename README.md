# laba1Tehn.Prog
#include <io.h>
#include <string>
#include <iostream>
#pragma warning(disable: 4996) 
using namespace std;



inline bool /*Перегруженный оператор */IsDirectory(const _finddata_t &FindData)
{
	return ((FindData.attrib & _A_SUBDIR) != 0); 
}
//struct?????????
struct _finddata_t FindData;   //уникальный дескриптор для вызова функций _findnext и _findclose
intptr_t    FindHandle; // структура, которая получает информацию об найденном файле или подкаталоге

class Iterator {
public:
	virtual void Next() { // определение виртуальной функции. Это означает, что эта функция может быть переопределена в производных классах
		cout << "Iterator\n";
	}
};
class first_d : public Iterator{
public:
	void Next() { // определение Next() применительно к first_d
		cout << "Name\n" << *FindData.name;
	}
};
class second_d : public Iterator {
public:
	void Next() { // определение Next() применительно к second_d
		cout << "Attribute file\n*" << FindData.attrib;
	}
};

/*
void printSubtree(const std::string& _root)
{
	struct _finddata_t findData;//struct?????????
	int handle = _findfirst((_root + "\\*.*").c_str(), &findData);
	int res = handle;
	while (res != -1)
	{
		std::string name(findData.name);
		if (name != "." && name != "..")
		{
			if (findData.attrib & _A_SUBDIR) //если это директория
			{
				printSubtree(_root + "\\" + name);
			}//if
			else
			{
				std::cout << _root << "\\" << name << std::endl;;
			}//else
		}//if

		res = _findnext(handle, &findData);

	}//while

}*/

//Функция DisplayFiles отображает файлы и каталоги, имена которых соответствуют указанной FileMask
void DisplayFiles(const string& FileMask)
{
	/* Функция _findfirst выполняет поиск в каталоге для файла или подкаталога, имя которого соответствует указанному имени
	FileMask.c_str () - указатель на строку с нулевым завершением, которая указывает действительный каталог
	или имя пути и файла, которое может содержать подстановочные знаки (* и?)
	FindHandle - уникальный дескриптор для последующего вызова функции _findnext (= -1L, если функция не работает)
	*/
	FindHandle = _findfirst(FileMask.c_str(), &FindData);
	if (FindHandle == -1L) //поиск файла  в текущей директории или //если в списке есть файл, то !=-1L
		return;

	do
	{
		cout << (IsDirectory(FindData) ? "Directory" : "File") << ": " <<
			FindData.name << endl;
	}

	/* Функция _findnext продолжает поиск файлов с предыдущего вызова на _findfirst
	 FindHandle - обработчик поиска, возвращенный предыдущим вызовом _findfirst
	 FindData - структура, которая получает информацию о найденном файле или подкаталоге
	 Функция возвращает 0, если она выполнена
	*/
	while (_findnext(FindHandle, &FindData) == 0);

	/* Функция _findclose закрывает указанный указатель поиска
	 FindHandle - дескриптор поиска файла, который должен был быть ранее открыт функцией _findfirst
	Дескриптор это то, через что можно обращаться к объекту*/
	_findclose(FindHandle);
}

int Iter_Next()
{
	Iterator base_obj;
	Iterator *p;
	first_d first_obj;
	second_d second_obj;
	p = &base_obj;
	p->Next(); // доступ к Next класса Iterator/ Поскольку  функция Next объявлена как виртуальная, то С++ определяет на этапе исполнения, какую из версий функции who() употребить, в зависимости от того, на какой объект указывает указатель р. 
	p = &first_obj; //Поскольку р указывает на объект типа first_d, то ис­пользуется соответствующая версия функции Next().
	p->Next(); // доступ к Next класса first_d
	p = &second_obj;//Аналогично, когда указателю р присвоен адрес объекта second_obj, то используется версия функции Next(), объявленная в классе second_d.
	p->Next(); // доступ к Next класса second_d
	return 0;
}

void main(int argc, char *argv[])
{
	system("chcp 1251");
	system("cls");
	string FileMask;

	if (argc > 1)
		FileMask = argv[1];
	else
	{
		cout << "Enter a drive, directory, and/or file mask to list ([drive:][path]filemask): ";//Введите диск, каталог и / или маску файла в список ([диск:] [путь] filemask)
		cin >> FileMask;
		if (!FileMask.length())
			return;
	}
    //printSubtree("C:\\Tmp");
	DisplayFiles(FileMask);
	Iter_Next;
}
