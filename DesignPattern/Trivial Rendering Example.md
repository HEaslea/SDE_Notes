```
#include <vector>
#include <cmath>
#include <stdexcept>
#include <algorithm>

struct Vector{  // requiring homogenous coordinates
	Vector() : x(0), y(0), z(0) w(1){}
	Vector(float a, float b, float c) : x(a), y(b), z(c), w(1) {}

	float Length() const 
	{ 
		return std::sqrt(x*x + y*y + z*z);
	}

	Vector Unit() const 
	{ 
		const float epsilon = 1e-6; // check to see if the vector is too small for unit length to apply

		float mag = Length(); 
		if(mag < epsilon){ 
			std::out_of_range e("");
			throw e;
		} 
		return *this/mag;
	}
	


}


```