```
#include <iostream>
#include <variant>

struct Fluid{};
struct LightItem{};
struct HeavyItem{};
struct FragileItem{};

struct VisitPackage 
{ 
	void operator()(Fluid&) { cout << "Fluid\n";}
	void operator()(LighItem&) { cout << "Light item\n";}
	void operator()(HeavyItem&) { cout << "Heavy item\n";}
	void operator()(FragileItem&) { cout << "Fragile Item\n";}
};

int main()) 
{
	std::variant<Fluid, LightItem, HeavyItem, FragileItem> package
	{ 
		FragileItem()
	};
	// passing in the functor type, closure type, operatable type
	// passing in a default one of those
	std::visit(VisitPackage(), package);
}
```