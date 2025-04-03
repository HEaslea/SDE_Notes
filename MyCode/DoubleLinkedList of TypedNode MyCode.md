Using polymorphism in order to add nodes that are a certain type: 
```
struct Node {
	Node* next;
	Node* prev;

	Node() : next(nullptr), prev(nullptr) {}
	virtual ~Node() {}

	virtual void print() const = 0;
};

template<typename T> // using polymorphism so that each node has different types
struct TypedNode : public Node {
	T data;

	TypedNode(T value) :  Node(), data(value) {}
	
	void print() const override{
		cout << data << endl; // provided the type is coutable
	}
};

struct DoubleLinkedList {

	Node* head;
	Node* tail;

	DoubleLinkedList() : head(nullptr), tail(nullptr) {}

	~DoubleLinkedList() 
	{
		clear(); // clearing everything
	}

	template<typename T> // does doesn't need to be template if we had a better system of creating nodes? 
	void Append(T value)
	{
		Node* newNode = new TypedNode<T>(value);
		if (!head) 
		{
			head = tail = newNode;
		}
		else {
			tail->next = newNode;  // set tail's next to new node
			newNode->prev = tail; // set newNodes prev to tail
			tail = newNode; // set the tail to be newNode
		}
	}

	template<typename T>
	void Prepend(T value)
	{
		Node* newNode = new TypedNode<T>(value);
		if (!head) {
			head = tail = newNode;
		}
		else {
			newNode->next = head;
			head->prev = newNode;
			head = newNode;
		}
	}

	void PrintForward() 
	{
		Node* current = head;
		while (current) {
			current->print();
			current = current->next;
		}
		cout << endl;
	}

	void clear() 
	{
		Node* current = head;
		while (current) {
			Node* nextnode = current->next;
			delete current; // deleting the object node, that contains the data, it's a value type, not a pointer type
			current = nextnode;
		}
		head = tail = nullptr;
	}
	
};

int main(){ 
// simple example
	DoubleLinkedList dL;
	dL.Append("Hugo");
	dL.Append(3);
	dL.Append(3.543);

	dL.PrintForward();
}
```



A more simple linked list: just forward; with a node counter too: 
```
struct Node; 
struct LinkedList; 


struct Node { 
	static size_t node_counter;
	int data;
	Node* next;
	size_t nc;

	Node() : data(0), next(nullptr), nc(node_counter)  { ++node_counter; }
	Node(int a) : data(a), next(nullptr), nc(node_counter) { ++node_counter; }

	void NodePrint() const {
		cout << nc << " : " << data << endl;
	}
};
size_t Node::node_counter = 1;


struct LinkedList {
	Node* head; Node* tail;

	LinkedList() : head(nullptr), tail(nullptr) {}

	LinkedList(Node* n) : head(n), tail(n) {} // if we are given a NodePointer that we want to use

	~LinkedList() 
	{
		Clear();
	}

	void Clear() 
	{
		auto current = head;
		while (current) {
			Node* nextnode = current->next;
			delete current;
			current = nextnode;
		}
		head = tail = nullptr;
		Node::node_counter = 1;
	}

	void Append(int a) 
	{
		auto newNode = new Node(a);
		if (!head) {
			head = tail = newNode; 
		}
		else {
			tail->next = newNode;
			tail = newNode;
		}
	}

	void PrintOut()
	{
		auto current = head;
		if (!current)
			cout << "List is Empty!\n";
		else {
			cout << "Printing List\n";
			while (current) {
				current->NodePrint();
				current = current->next;
			}
		}
		cout << endl;
	}
};
```