# Tips and Tricks C#

Basic Math - Add/subtract or increment/decrement assignment
public int number = 10;

// Full (adds one to variable)
number = number + 1;

// Shortened
number += 1;

// Shortest
number++;
The shortened version also supports other math operations.

String Interpolation
String Interpolation is an easier way to build strings out of multiple pieces.

void Start()
{
	var name = "Mark";
	var age = 25;
	var day = "Tuesday";
	
	// Composite - hard to know what you are writing
	Debug.LogFormat("My name is {0}. I am {1}. Today is {2}.", name, age, day);
	
	// Concatenation - too much syntax
	Debug.Log("My name is " + name + ". I am " + age.ToString() + ". Today is " + day + ".");
	
	// Interpolation - easy to understand
	Debug.Log($"My name is {name}. I am {age}. Today is {day}.");
}

Expression-bodied members
A C#6 feature that is syntactic sugar (designed to make things easier to read or express).

// Long Methods
void Method()
{
	Debug.Log("Hello");
}

float MethodWithReturn()
{
	return 100f;
}

// Shortened Methods
void Method() => Debug.Log("Hello");
float MethodWithReturn() => 100f;

// Long Property
public string Name
{
	get { return name; }
	set { name = value; }
} 

// Shortened Property
public string Name
{
	get => name;
	set => name = value;
} 

// Long read-only Property
public string Name
{
	get { return name; }
}

// Shortened read-only Property
public string Name => name;

Lambda expressions
Shorthand syntax for anonymous delegates - C# Programming Guide.

public Action<float> OnSomethingHappened;

void Start()
{
	// Long event subscription - requires creation of the DoWhenSomethingHappened method
	OnSomethingHappened += DoWhenSomethingHappened;
	
	// Shortened event subscription - anonymous function, written inline.
	OnSomethingHappened += f => Debug.Log(f);
	
	// Shortened event subscription - example of required syntax when there are no parameters, or two or more parameters.
	OnSomethingHappened += () => Debug.Log("Hello");
	OnSomethingHappened += (param1, param2) => Debug.Log($"{param1} {param2}");
}

// Long event handler method
void DoWhenSomethingHappened(float f)
{
    Debug.Log(f);
}
  
var keyword
The var keyword allows you to create variables without telling the compiler what type it is. The compiler will infer the type.

// Long local variable declaration
List<LongTypeNameGoesHere> variableName = new List<LongTypeNameGoesHere>();

// Shortened local variable declaration
var variableName = new List<LongTypeNameGoesHere>();
  
// Long foreach loop
foreach(GameObject go in GameObjectList)
{
    Debug.Log(go.name);
}

// Shortened foreach loop
foreach(var go in GameObjectList)
{
    Debug.Log(go.name);
}
  
Method Chaining
Method chaining can be achieved when a Method returns the instance of the Class it’s inside.

class Test
{
    public Test MethodOne { return this; }
    public Test MethodTwo { return this; }
    public Test MethodThree { return this; }
	
	void HowToUse()
	{
		MethodOne().MethodTwo().MethodThree();
	}
}

class AnotherClass
{
	var test = new Test() // Note: no semi-colon here.
		.MethodOne()
		.MethodTwo()
		.MethodThree();
}
  
They can be used to create Fluent Interface API’s.

There is an excellent Finite State Machine and Behaviour Tree for Unity that do this. See the associated article.

Cast Interface to MonoBehaviour
When working with Interfaces that are implemented on MonoBehaviours, you will not be able to access the Component or GameObject the Interface is on.

To allow this, you must cast it.

public class PlayerComponent : MonoBehaviour, IInteractor
{
	IInteractor _interactorInterface;

	void Awake()
	{
		_interactorInterface = this;

		var test = _interactorInterface as MonoBehaviour;

		// Prints "PlayerGameObject"
		Debug.Log($"GameObject name is {test.gameObject.name}");
		// Prints "PlayerComponent"
		Debug.Log($"Component type is {test.GetType()}");
	}
	
	// Also works the other way, from Component to Interface
	PlayerComponent _playerComponent;
	
	void Start()
	{
		_playerComponent = this;
		
		var test = _playerComponent as IInteractor;
		
		test.Interact();
	}
}
  
public interface IWeapon
{
	void Fire();
}

public class WeaponComponent : MonoBehaviour, IWeapon
{
    void Fire() { Debug.Log("Firing!"); }
}

public class AttackSystem : MonoBehaviour
{
	// Assigned from another script, for example:
	// Example 1: attackSystem.CurrentlyEquipped = instanceOfPrefab.GetComponent<WeaponComponent>();
	// Example 2: attackSystem.CurrentlyEquipped = instanceOfPrefab.GetComponent<IWeapon>();
	IWeapon _currentlyEquippedWeapon;
	
	IWeapon CurrentlyEquipped { get => _currentlyEquippedWeapon; set => _currentlyEquippedWeapon = value; }
	
	void Update()
	{
		if (Input.GetMouseButtonDown(0))
		{
			_currentlyEquippedWeapon.Fire();
			
			// PROBLEM:
			// I want to print the name of the GameObject that has the WeaponComponent on it, but how?
			// _currentlyEquippedWeapon has no access to MonoBehaviour methods/properties, it can only access Fire().
			Debug.Log("?")
			
			// So we create a new variable and cast it to MonoBehaviour.
			var weaponBehaviour = _currentlyEquippedWeapon as MonoBehaviour;
			
			// It works because IWeapon is implemented on the WeaponComponent MonoBehaviour. Otherwise it will be null.
			if (weaponBehaviour != null)
			{
				Debug.Log($"{weaponBehaviour.gameObject.name} fired!")
			}
		}
	}
}
