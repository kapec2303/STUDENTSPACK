#include <iostream>
#include "vector"
#include "string"
#include "numeric"

using namespace std;

enum Subjects {
    Math, English, Chemistry, Russian
};

struct Marks {
    vector<unsigned int> Math;
    vector<unsigned int> English;
    vector<unsigned int> Chemistry;
    vector<unsigned int> Russian;
};

class Student {
private:
    string name;
public:
    Marks marks;

    Student(const string &name) : name(name) {}

    const string &getName() const {
        return name;
    }

    void setName(const string &name) {
        Student::name = name;
    }

//    const Marks &getMarks() const {
//        return marks;
//    }
//
//    void setMarks(const Marks &marks) {
//        Student::marks = marks;
//    }

void getMark(int mark, Subjects subject ) {
        switch (subject) {
            case Math: {
                this->marks.Math.push_back(mark);
                break;
            }
            case English: {
                this->marks.English.push_back(mark);
                break;
            }
            case Chemistry: {
                this->marks.Chemistry.push_back(mark);
                break;
            }
            case Russian: {
                this->marks.Math.push_back(mark);
                break;
            }
        }
    };

    bool isOtlichnik1(vector<unsigned int>& marks) { //по одному предмету
        if (marks.empty()) {
            return true;
        }
        float average = accumulate(marks.begin(), marks.end(), 0.0) / marks.size();
        return average >= 4.5;
    }

    bool isOtlichnik() {
        if (!isOtlichnik1(this->marks.Math)) return false;
        if (!isOtlichnik1(this->marks.English)) return false;
        if (!isOtlichnik1(this->marks.Chemistry)) return false;
        if (!isOtlichnik1(this->marks.Russian)) return false;
        return true;
    }
};

int main() {
    Student s1("Vasya");
    Student s2("Katya");
    s1.getMark(5, Math);
    s2.getMark(2, Russian);
    cout << s1.isOtlichnik() << endl;
    cout << s2.isOtlichnik() << endl;
    return 0;
}
