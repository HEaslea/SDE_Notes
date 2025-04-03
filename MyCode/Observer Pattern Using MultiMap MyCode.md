```
using namespace std;
class Observer; class iObserver;
class Subject; class iSubject;

// foward list of observers?

enum class ObserverType {SOUND, ACHIEVEMENT, PHYSICS, LOG};

class Observer {
public:

	Observer(const string& n) : name(n) {}
	virtual void onNotify() = 0;
	virtual ~Observer() {}

	string name;
};


class SoundObserver : public Observer {
public: 
	SoundObserver() = delete;
	SoundObserver(const string& n) : Observer(n) {}
	~SoundObserver() { cout << name << " Destroyed!" << endl; }

	void onNotify() override {
		cout << "Sound Observer " << name << " notified!" << endl;
	}
};

class PhysicObserver : public Observer {
public: 	
	PhysicObserver() = delete;
	PhysicObserver(string n) : Observer(n) {}
	~PhysicObserver() { cout << name << " Destroyed!" << endl; }

	void onNotify() override {
		cout << "Physic Observer " << name << " notified!" << endl;
	}
};


class Subject {
public:
	virtual ~Subject() {}
	virtual void NotifyAll() = 0;
};

class iSubject : public Subject {
public:
	std::multimap<ObserverType, shared_ptr<Observer>> o_map;
	string name;
	
	iSubject(string n) : name(n) {}

	void NotifyAll() override 
	{
		for (auto& c : o_map) {
			c.second->onNotify(); // iObserver on notify // add to iObserver
		}
	}

	void NotifyType(ObserverType type)
	{
				//for (auto it = o_map.begin(); it != o_map.end(); ++it)
		//{
		//	if (it->first == type)
		//	{
		//		it->second->onNotify();// actually disgusting
		//	}
		//}

		auto range = o_map.equal_range(type);
		for (auto it = range.first; it != range.second; ++it) {
			it->second->onNotify();
		} // this second one is on a specific range


	}

	void AddObserver(ObserverType type, shared_ptr<Observer> op) 
	{
		o_map.insert(make_pair(type, op)); // now he's happy :)
	}
};


int main() {
	auto pO = make_shared<PhysicObserver>("PhyO 1");
	auto sO = make_shared<SoundObserver>("SouO 1");
	auto sO2 = make_shared<SoundObserver>("SouO 2");

	iSubject newSub("Hugo's Sub");
	newSub.AddObserver(ObserverType::PHYSICS, pO);
	newSub.AddObserver(ObserverType::SOUND, sO);
	newSub.AddObserver(ObserverType::SOUND, sO2);

	newSub.NotifyType(ObserverType::SOUND); 
	// annoyingly, this will go through the entire thing as well

	cout << endl;

	newSub.NotifyAll(); // go through the entire thing
```
