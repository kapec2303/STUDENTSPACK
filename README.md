#include <iostream>
#include "vector"
#include "string"
#include <numeric>
#include <ctime>
#include <algorithm>


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

    bool operator==(const Student& other) const {
        return this->name == other.name;
    }
};



class Teacher {
private:
    string name;
protected:
    Subjects aaa;
    int MoodCount = 0;
    int MoodMax = 4;
public:
    bool Mood =(rand() % 2 == 0);

    Teacher(const string &name, Subjects aaa) : name(name), aaa(aaa) {}

    Teacher(const string &name, Subjects aaa, int moodMax) : name(name), aaa(aaa), MoodMax(moodMax) {}

    const string &getName() const {
        return name;
    }

    virtual void giveMark(Student& s, unsigned int a) {
        if (MoodCount < MoodMax) {
            s.getMark(a, aaa);
            MoodCount += 1;
        } else {
            a = rand() % 4 + 2;
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

    bool operator==(const Teacher& other) const {
        return this->name == other.name && this->aaa == other.aaa;
    }
};

class OneTeacher : public Teacher { //Ставит только одну оценку: либо пять, либо 2
private:
    string name;
public:
    bool isGood = 0;

    const string &getName1() const {
        return name;
    }

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

    //==

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

    const Teacher &getSt() const {
        return st;
    }

    const vector<Student> &getHere() const {
        return here;
    }

    void WorkOnLesson() {
        int skolko = rand() % here.size();
        for (int i=0; i < skolko; i++) {
            Student student = here[rand() % here.size()];
            st.MoodMark(student);
            cout << "Student " << student.getName() << " got a Mark!"<< endl;
        }
    }

    bool operator==(const Lesson &rhs) const {
        return st == rhs.st &&
               here == rhs.here;
    }

    bool operator!=(const Lesson &rhs) const {
        return !(rhs == *this);
    }
};

class Parent {
private:
    string name;
    bool Mood;
    vector<Student> children;
public:
    Parent(const string &name, bool mood) : name(name), Mood(mood) {}

    const string &getName() const {
        return name;
    }

    bool isMood() const {
        return Mood;
    }

    const vector<Student> &getChildren() {
        return children;
    }

    void addChild(const Student& child) {
        children.push_back(child);
    }

    bool isMyChild(Student& student) {
        auto it = find(children.begin(), children.end(), student);
        if (it != children.end()) {
            return true;
        } else {
            return false;
        }
    }


    void tellAboutChild(Student& student) {
        string Otlich;
        if (student.isOtlichnik()) {
            Otlich = "has good marks :)";
        } else Otlich = "has bad marks :(";
        if (this->isMyChild(student)) {
            cout << "Hello! I'm " << this->getName() << ", " << student.getName() << "'s parent." << endl;
            cout << "This child " << Otlich << endl;
        } else {
            cout << "This iss not my child..." << endl;
        }
    }

    void tellAboutRandomChild() {
        if (children.empty()) {
            cout << "I don't have any children :O" << endl;
        return;
        } else {
            unsigned int iii = rand() % children.size();
            tellAboutChild(children[iii]);
        }
    }

    void tellAboutSpecChild(Student student) {
        if (isMyChild(student)) {
            tellAboutChild(student);
        } else {
            cout << "Excuse me, this is not my child..." << endl;
        }
    }

    virtual void tellAboutAllChildrenShort() {
        for (Student& student: children) {
            if (student.isOtlichnik() && Mood) {
                cout << "I love all of my children! They are amazing!\n";
            } else {
                cout << "I don't feel like talking about my children today\n";
            }
        }
    }

    void tellAboutAll() {
        for (Student& student: children) {
            tellAboutChild(student);
            cout << endl;
        }
    }
};

class Meeting {
private:
    vector<Teacher> teacherHere;
    vector<Parent> parentHere;
    vector<Student> studToDis;
    vector<Lesson> discussLesson;
    vector<Teacher> absentTeacher;
    vector<Student> sWithAbsentParent;

public:

    Meeting() {}

    void addParent(vector<Parent>& p) {
        parentHere.insert(parentHere.end(), p.begin(), p.end());
    }
    void addTeacher(vector<Teacher>& t) {
        teacherHere.insert(teacherHere.end(), t.begin(), t.end());
    }
    void addLesson(vector<Lesson>& l) {
        discussLesson.insert(discussLesson.end(), l.begin(), l.end());
    }

    void KtoVel() {
        for (const Lesson &lesson: discussLesson) {
            Teacher lessonTeacher = lesson.getSt();
            if (find(teacherHere.begin(), teacherHere.end(), lessonTeacher) == teacherHere.end()) {
                absentTeacher.push_back(lessonTeacher);
            }
        }
    }

    void Roditeli() {
        for (Student& student: studToDis) {
            bool parentFound = false;

            for (Parent &parent: parentHere) {
                if (parent.isMyChild(student)) {
                    parentFound = true;
                    break; // Родитель найден, переходим к следующему ученику
                }
            }

            if (!parentFound) {
                // Добавить ученика в список, если его родитель не найден
                sWithAbsentParent.push_back(student);
            }
        }
    }

    void conductMeeting() {
        KtoVel();
        Roditeli();
        discussStudents();
        generateReport();
    }

    void discussStudents() {
        for (const Student &student: studToDis) {
            if (std::find(sWithAbsentParent.begin(), sWithAbsentParent.end(), student) == sWithAbsentParent.end()) {
                cout << "Discussing student " << student.getName() << endl;
            } else {
                cout << "Student " << student.getName() << " is not being discussed because his parent is absent today\n" << endl;
            }
        }
    }

    void generateReport() {
        cout << "Report about meeting:" << endl;
        cout << "Apsent teachers:" << endl;
        for (const Teacher &teacher: absentTeacher) {
            cout << teacher.getName() << endl;
        }
        cout << "Students with apsent parents:" << endl;
        for (const Student &student: sWithAbsentParent) {
            cout << student.getName() << endl;
        }
    }
//треш

};

class Grandma : public Parent {
public:
    Grandma(const string &name, bool mood) : Parent(name, mood) {}

    void TellAboutVnuk(Student& student) {
        cout << "My little grandchild " << student.getName() << "is the best! ^-^" << endl;
    }

    void tellAboutAll() {
        cout << "All of my grandchildren are my treasures!" << endl;
    }

    void tellAboutOther(Student& student) {
        if (this->isMood() == 1) {
            cout << "Idk... This student is OK." << endl;
        } else {
            cout << "This student is a shame to the family! Ew" << endl;
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

    Parent P1("Natalia", 1);
    P1.addChild(s1);
    P1.addChild(s2);
    P1.tellAboutAll();
    Parent P2("Lena", 0);
    Grandma g1("Nina", 1);
    g1.tellAboutOther(s3);
    vector<Lesson> lessons;
    lessons.push_back(para);
    Meeting sobranie;
    sobranie.addLesson(lessons);
    vector<Teacher> teachers;
    teachers.push_back(t1);
    sobranie.addTeacher(teachers);
    vector<Parent> parents;
    parents.push_back(P1);
    parents.push_back(P2);
    sobranie.generateReport();
    return 0;
    }
