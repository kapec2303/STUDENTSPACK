#include <iostream>
#include "vector"
#include "string"
#include "numeric"
#include <ctime>

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
                this->marks.Russian.push_back(mark);
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

class Teacher {
private:
    string name;
protected:
    Subjects aaa;
    int MoodCount = 0;
public:
    bool Mood =(rand() % 2 == 0);

    Teacher(const string &name, Subjects aaa) : name(name), aaa(aaa) {}

    virtual void giveMark(Student& s, unsigned int a) {
        if (MoodCount < 4) {
            s.getMark(a, aaa);
            MoodCount += 1;
        } else {
            a = rand() % 6;
            //cout << a << endl;
            if (a <=1) {
                a += 2;
            }
            s.getMark(a, aaa);
            MoodCount = 0;
        }
    }

    void MoodMark(Student& s) {
        bool Otlichnik = s.isOtlichnik();
        unsigned int r = (rand() % 2 == 0);

        if (Mood && Otlichnik) {
            giveMark(s, 5);
        }
        if (!Mood && Otlichnik) {
            giveMark(s, (4+r));
        }
        if (Mood && !Otlichnik) {
            giveMark(s, 4);
        }
        if (!Mood && !Otlichnik) {
            giveMark(s, (2+r));
        }
    }
};

class OneTeacher : public Teacher { //Ставит только одну оценку: либо пять, либо 2
public:
    bool isGood = 0;

    OneTeacher(const string &name, Subjects aaa, bool isGood) : Teacher(name, aaa), isGood(isGood) {}

    bool isGood1() const {
        return isGood;
    }

    void giveMark(Student& s, unsigned int a) override {
        bool b = this->isGood1();
        if (b) {
            Teacher::giveMark(s, 5);
        } else {
            Teacher::giveMark(s, 2);
        }
    }

};

class Lesson {
private:
    Teacher st;
public:

    Lesson(const Teacher &st) : st(st) {}

    vector<Student> here;

    void addToLesson(Student student) {
        here.push_back(student);
    }

    void WorkOnLesson() {
        int skolko = rand() % here.size();
        for (int i=0; i < skolko; i++) {
            Student student = here[rand() % here.size()];
            st.MoodMark(student);
            cout << "Student " << student.getName() << " got a Mark!"<< endl;
        }
    }
};


int main() {
    srand(time(0));

    Student s1("Vasya");
    Student s2("Katya");
    s1.getMark(5, Math);
    s2.getMark(2, Russian); //этап 1

    Teacher t1("Efremov", English);
    t1.giveMark(s1, 3); //этап 2

    t1.MoodMark(s2); //этап 3

    Student s3("Lola");
    Student s4("Sasha");
    Student s5("Petya");
    Lesson para(t1); //Ефремов - препод на этой паре
    para.addToLesson(s1);
    para.addToLesson(s2);
    para.addToLesson(s3);
    para.addToLesson(s4);
    para.addToLesson(s5);
    //para.WorkOnLesson();

    OneTeacher t2("Stepanova5", English, 1); //Добрая
    //t1.giveMark(s5, 5);
    t2.giveMark(s5, 5);
    OneTeacher t3("Stepanova2", Chemistry, 0); //злая
    t3.giveMark(s4, 5);

    Teacher M1("Aaaaa", Chemistry);
    M1.giveMark(s1, 5);
    M1.giveMark(s1, 5);
    M1.giveMark(s1, 5);
    M1.giveMark(s1, 5);
    M1.giveMark(s1, 5);
    M1.giveMark(s1, 5);
    M1.giveMark(s1, 5);
    M1.giveMark(s1, 5);
    M1.giveMark(s1, 5);
    M1.giveMark(s1, 5);
        cout << "Final marks for all students:" << endl;
        for (const Student& student : {s1, s2, s3, s4, s5}) {
            cout << "Student " << student.getName() << ":" << endl;
            cout << "   Math: ";
            for (const auto& mark : student.marks.Math) {
                cout << mark << " ";
            }
            cout << endl;

            cout << "   English: ";
            for (const auto& mark : student.marks.English) {
                cout << mark << " ";
            }
            cout << endl;

            cout << "   Chemistry: ";
            for (const auto& mark : student.marks.Chemistry) {
                cout << mark << " ";
            }
            cout << endl;

            cout << "   Russian: ";
            for (const auto& mark : student.marks.Russian) {
                cout << mark << " ";
            }
            cout << endl;

            cout << "----------------------" << endl;
        }
        t1.giveMark(s1, 5);
    return 0;
    }
