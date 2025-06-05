#include <iostream>
#include <fstream>
#include <exception>
#include <vector>
#include <algorithm>
using namespace std;

// Шаблонный класс списка
template <typename T>
class List {
private:
    struct Node {
        T data;
        Node* next;
        Node* prev;
    };
    Node* head;
    Node* tail;
public:
    List() : head(nullptr), tail(nullptr) {}
    ~List() { clear(); }

    void add(T value) {
        Node* newNode = new Node{value, nullptr, tail};
        if (!tail) head = newNode;
        else tail->next = newNode;
        tail = newNode;
    }

    void search(T value) {
        Node* temp = head;
        while (temp) {
            if (temp->data == value) {
                cout << "Элемент найден: " << value << endl;
                return;
            }
            temp = temp->next;
        }
        throw runtime_error("Элемент не найден!");
    }

    void remove(T value) {
        Node* temp = head;
        while (temp) {
            if (temp->data == value) {
                if (temp->prev) temp->prev->next = temp->next;
                if (temp->next) temp->next->prev = temp->prev;
                if (temp == head) head = temp->next;
                if (temp == tail) tail = temp->prev;
                delete temp;
                return;
            }
            temp = temp->next;
        }
        throw runtime_error("Удаляемый элемент не найден!");
    }

    void saveToFile(const string& filename) {
        ofstream file(filename);
        if (!file) throw runtime_error("Ошибка при открытии файла!");
        Node* temp = head;
        while (temp) {
            file << temp->data << endl;
            temp = temp->next;
        }
        file.close();
    }

    void loadFromFile(const string& filename) {
        ifstream file(filename);
        if (!file) throw runtime_error("Ошибка при открытии файла!");
        T value;
        while (file >> value) add(value);
        file.close();
    }

    void sortList() {
        vector<T> vec;
        Node* temp = head;
        while (temp) {
            vec.push_back(temp->data);
            temp = temp->next;
        }
        sort(vec.begin(), vec.end());
        clear();
        for (T val : vec) add(val);
    }

    void compare(T a, T b) {
        cout << "Сравнение: " << a << " vs " << b << " -> " << (a == b ? "равны" : "не равны") << endl;
    }

    void display() {
        Node* temp = head;
        while (temp) {
            cout << temp->data << " ";
            temp = temp->next;
        }
        cout << endl;
    }

    void clear() {
        while (head) {
            Node* temp = head;
            head = head->next;
            delete temp;
        }
        tail = nullptr;
    }
};

int main() {
    try {
        List<int> lst;
        lst.add(10);
        lst.add(20);
        lst.add(30);
        
        lst.display();
        lst.search(20);
        
        lst.remove(10);
        lst.display();
        
        lst.saveToFile("data.txt");
        lst.clear();
        lst.loadFromFile("data.txt");
        lst.display();
        
        lst.sortList();
        lst.display();
        
        lst.compare(20, 30);
    } catch (const exception& e) {
        cout << "Ошибка: " << e.what() << endl;
    }
    
    return 0;
}
