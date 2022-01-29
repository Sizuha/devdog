# 디자인 패턴에서 배우는 설계 원칙
- if(switch 포함) 대신 상속과 다형성 활용한다
- 인터페이스 중심 설계
  - 변경이 발생될 부분에 대해, 사용자를 위한 공통된 인터페이스를 제공하고, 세부적인 내용은 그 인터페이스를 상속 받아서 구현하여 미래의 변동사항에 대비한다.

# 객체 생성에 관한 패턴
## 공통의 문제상황 : 예시
게임에서 사용되는 게임 캐릭터를 정의한 Character Class가 있다.

게임 캐릭터는 전사(Fighter), 궁수(Archer), 마법사(Wizard)로 각각 직업이 구분되어 있다.

플레이어는 이들 직업 중에 하나를 선택해서 플레이 하게 된다.

이것을 구현하기 위한 적절한 설계를 찾아보자.

## Abstract Factory
### 문제 상황
- 제품군의 존재
  - 여러 종류의 객체들이 존재하는 상태에서,그 객체들이 다시 여러 종류의 제품군으로 그룹화 되는 경우
- 제품군이 조합되어지는 경우의 수가 많아질 경우에 대한 문제.

### 구현
앞에 나온 공통의 문제상황을 예로 들면,
```csharp
// 게임 캐릭터에 대한 공통의 정의
class Character {
	public Vector2 position; // 캐릭터의 현재 위치
	public int hp; 

	public Character(int hp) {
		this.hp = hp;
	}

	public float getDistanceFrom(Character target) {
		return Math.sqrt( Math.pow(target.position.x - this.position.x, 2) + Math.pow(target.position.y - this.position.y, 2) );
	}

	public void defense(int damage) {
		hp -= damage;
	}	
}


interface Weapon {
	int getAttackRange();
	int getDamage();
}

class Sword : Weapon {
	public int getAttackRange() { return 1; }
	public int getDamage() { return 10; }
}

class Bow : Weapon {
	public int getAttackRange() { return 5; }
	public int getDamage() { return 5; }	
}

class Staff : Weapon {
	public int getAttackRange() { return 5; }
	public int getDamage() { return 5; }	
}


interface Defender {
	int getDef();
}

class NoneDefense : Defender {
	public int getDef() { return 0; }
}

class Shield : Defender {
	public int getDef() { return 2; }
}

class MagicShield : Defender {
	public int getDef() { return 1; }
}


//----- 플레이어의 액션을 수행하는 부분 -------
class GameManager {

	public void attack(Character atkChr, Character defChr) {
		var distance = atkChr.getDistanceFrom(defChr);
		if (distance <= atkChr.weapon.getAttackRange()) {
			var damage = atkChr.weapon.getDamage() - defChr.defender.getDef();
			defChr.defense(damage);
		}
	}

}
```

이제 플레이어가 자신이 선택한 직업에 대해서, 그에 맞는 캐릭터를 생성하고, 직업별로 무기와 방어구를 생성해 주어야 한다. 하지만, 그외 공격이나 방어에 대한 로직을 변함이 없다.

이때 캐릭터 직업을 하나의 제품군으로 보고, 각 직업별로 팩토리 클래스를 하나씩 정의해 나간다.
```csharp
// 제품군에 대한 인터페이스
interface PlayerJobFactory {
	Character createCharacter();
	Weapon createWeapon();
	Defender createDefender();
}

class FighterFactory : PlayerJobFactory {
	public Character createCharacter() { new Character(200); }
	public Weapon createWeapon() { new Sword(); }
	public Defender createDefender() { new Shield(); }
}

class ArcherFactory : PlayerJobFactory {
	public Character createCharacter() { new Character(150); }
	public Weapon createWeapon() { new Bow(); }
	public Defender createDefender() { new NoneDefense(); }
}

class WizardFactory : PlayerJobFactory {
	public Character createCharacter() { new Character(100); }
	public Weapon createWeapon() { new Staff(); }
	public Defender createDefender() { new MagicShield(); }
}
```

## Factory Method
### 문제 상황
- 어느 한 클래스 내에서 사용되는 대상 객체가 여러 종류가 있고,
- 그 중에서 경우에 따라 하나를 선택해서 생성하게 되는 경우
  - 즉, 객체 생성과정에서 경우의 수가 발생
 
### 구현
상위의 추상 클래스에서 객체 생성을 위한 팩토리 메소드 인터페이스를 만들어 두고, 실제 객체의 생성은 하위 클래스의 팩토리 메소드에서 처리하게 한다.

```csharp
// 게임 캐릭터에 대한 공통의 정의
abstract class Character {
	public Vector2 position; // 캐릭터의 현재 위치
	public int hp; 

	Weapon weapon;
	Defender defender;

	public Character(int hp) {
		this.hp = hp;

		weapon = createWeapon();
		defender = createDefender();
	}

	abstract protected Weapon createWeapon();
	abstract protected Defender createDefender();

	public float getDistanceFrom(Character target) {
		return Math.sqrt( Math.pow(target.position.x - this.position.x, 2) + Math.pow(target.position.y - this.position.y, 2) );
	}

	public void defense(int damage) {
		hp -= damage - defender.getDef();
	}	

	public void attack(Character defChr) {
		var distance = getDistanceFrom(defChr);
		if (distance <= weapon.getAttackRange()) {
			var damage = weapon.getDamage();
			defChr.defense(damage);
		}
	}

}


//---- 직업별 정의 -------
class Fighter : Character {
	public Fighter() : base(200) {}
	protected Weapon createWeapon() { return new Sword();}
	protected Defender createDefender() { new Shield(); }
}

class Archer : Character {
	public Archer() : base(150) {}
	protected Weapon createWeapon() { return new Bow();}
	protected Defender createDefender() { new NoneDefense(); }	
}

class Wizard : Character {
	public Wizard() : base(100) {}
	protected Weapon createWeapon() { return new Staff();}
	protected Defender createDefender() { new MagicShield(); }	
}
```

## Builder
### 문제 상황
- 객체 생성에 필요한 정보를 수집하는 시점과, 실제 객체를 생성해야 되는 시점에 격차가 있는 경우,
  - 객체 생성 과정을 단계적으로 진행하게 해야되는 경우
- 객체 생성 과정에서, 다양한 옵션을 부여해야 되는 경우

### 구현
- 목적이 되는 객체를 생성하기 위한 빌더 클래스를 구현한다.
- 빌더 클래스는 목적 객체를 만들기 위한 정보를 셋팅하기 위한 인터페이스를 제공.
- 최종적으로 직전까지 수집된 정보를 바탕으로 목적 객체를 생성하기 위한 메소드를 제공한다.

```csharp
class Character {
	public string name;
	public Vector2 position; // 캐릭터의 현재 위치
	public int hp; 

	public Weapon weapon;
	public Defender defender;

	public Character(string name, Vector2 position, int hp, Weapon weapon, Defender defender) {
		// ...
	}

	public float getDistanceFrom(Character target) {
		return Math.sqrt( Math.pow(target.position.x - this.position.x, 2) + Math.pow(target.position.y - this.position.y, 2) );
	}

	public void defense(int damage) {
		hp -= damage - defender.getDef();
	}	

	public void attack(Character defChr) {
		var distance = getDistanceFrom(defChr);
		if (distance <= weapon.getAttackRange()) {
			var damage = weapon.getDamage();
			defChr.defense(damage);
		}
	}

}


class ChracterBuilder {

	string name;
	int hp;
	Vector2 pos;
	Weapon w;
	Defender d;

	public ChracterBuilder() {}

	public ChracterBuilder setName(string name) { this.name = name; return this; }
	public ChracterBuilder setHP(int hp) { this.hp = hp; return this; }
	public ChracterBuilder setPosition(Vector2 pos) { this.pos = pos; return this; }
	public ChracterBuilder setWeapon(Weapon weapon) { w = weapon; return this; }
	public ChracterBuilder setDefender(Defender defender) { d = defender; return this; }

	public Character createCharacter() {
		return new Character(name, pos, hp, w, d);
	}

}


// 사용예:
void createFighter() {
	Character player =
		new ChracterBuilder()
		.setName("Player")
		.setHP(200)
		.setWeapon(new Sword())
		.setDefender(new Shield())
		.createCharacter();

	// . . .
}
```

# MVC

MVCの典型的な相関図
MVC（Model View Controller モデル・ビュー・コントローラ）は、ユーザーインタフェースをもつアプリケーションソフトウェアを実装するためのデザインパターンである。

アプリケーションソフトウェアの内部データを、ユーザーが直接参照・編集する情報から分離する。そのためにアプリケーションソフトウェアを以下の3つの部分に分割する。

- model: アプリケーションデータ、ビジネスルール、ロジック、関数
- view: グラフや図などの任意の情報表現
- controller: 入力を受け取りmodelとviewへの命令に変換する
