---
category: GO
---
## 简介
有限状态机（Finite-state machine，简写：FSM）又可以称作有限状态自动机，简称状态机。

它必须是可以附着在某种事物上的，且该事物的状态是有限的，通过某些触发事件，会让其状态发生转换。为此，**有限状态机就是描述这些有限的状态和触发事件及转换行为的数学模型。**

以下来自[csdn](https://blog.csdn.net/weixin_42117918/article/details/90764268)
任何一个FSM都可以用状态转换图来描述，图中的节点表示FSM中的一个状态，有向加权边表示输入字符时状态的变化。*如果图中不存在与当前状态与输入字符对应的有向边，则FSM将进入“消亡状态(Doom State)”，此后FSM将一直保持“消亡状态”。* **状态转换图中还有两个特殊状态：状态1称为“起始状态”，表示FSM的初始状态。状态6称为“结束状态”，表示成功识别了所输入的字符序列。**

在启动一个FSM时，首先必须将FSM置于“起始状态”，然后输入一系列字符，最终，FSM会到达“结束状态”或者“消亡状态”。
<img src="/my_pic/finnite state machine.png">

说明：

在通常的FSM模型中，一般还存在一个“接受状态”，并且FSM可以从“接受状态”转换到另一个状态，只有在识别最后一个字符后，才会根据最终状态来决定是否接受所输入的字符串。此外，也可以将“起始状态”也作为接受状态，因此空的输入序列也是可以接受的。

## 特性
状态总数（state）是有限的。
任一时刻，只处在一种状态之中。
某种条件下，会从一种状态转变（transition）到另一种状态。

## FSM的实现
程序设计思路大致如下：

使用状态转换图描述FSM
状态转换图中的结点对应不同的状态对象
每个状态对象通过一个输入字符转换到另一个状态上，或者保持原状态不变。

## 示例
```go
package main

import (
    "context"
    "fmt"

    "github.com/looplab/fsm"
)

func main() {
    fsm := fsm.NewFSM(
        "closed",
        fsm.Events{
            {Name: "open", Src: []string{"closed"}, Dst: "open"},
            {Name: "close", Src: []string{"open"}, Dst: "closed"},
        },
        fsm.Callbacks{
        	//简短格式
        	"close": func(ctx context.Context, event *fsm.Event) {
				fmt.Println("called after event named  ", event.Event)
			},
			//简短格式
			"open": func(ctx context.Context, event *fsm.Event) {
				fmt.Println("called after entering ", event.Event)
			},
        },
    )

    fmt.Println(fsm.Current())

    err := fsm.Event(context.Background(), "open")
    if err != nil {
        fmt.Println(err)
    }

    fmt.Println(fsm.Current())

    err = fsm.Event(context.Background(), "close")
    if err != nil {
        fmt.Println(err)
    }

    fmt.Println(fsm.Current())
}
```  
结果
closed
called after entering  open
open
called after event named   close
closed
  

函数NewFSM利用events和callbacks来创建FSM,第一个参数initial就是初始状态，events就是事件的集合(事件name,source state可以是多个 ,dest state)，callbacks是回调函数集合map,与事件名/状态名相关。


1. before_<EVENT> - called before event named <EVENT>

2. before_event - called before all events

3. leave_<OLD_STATE> - called before leaving <OLD_STATE>

4. leave_state - called before leaving all states

5. enter_<NEW_STATE> - called after entering <NEW_STATE>

6. enter_state - called after entering all states

7. after_<EVENT> - called after event named <EVENT>

8. after_event - called after all events

There are also two short form versions for the most commonly used callbacks. They are simply the name of the event or state:

1. <NEW_STATE> - called after entering <NEW_STATE>

2. <EVENT> - called after event named <EVENT>

If both a shorthand version and a full version is specified it is undefined which version of the callback will end up in the internal map. This is due to the pseudo random nature of Go maps. No checking for multiple keys is currently performed.
如果简短格式和标准格式的callback都定义了，哪个会最终执行是不确定的。

```go
 Callbacks{         
 	 "before_warn": func(_ context.Context, e *Event) {             
 	 	fmt.Println("before_warn")         
 	  },         
 	 "before_event": func(_ context.Context, e *Event) {             
 	 	fmt.Println("before_event")         
 	 },         
 	 "leave_green": func(_ context.Context, e *Event) {
 	    fmt.Println("leave_green")         
 	  },         
 	 "leave_state": func(_ context.Context, e *Event) {
 	    fmt.Println("leave_state")        
 	  },         
 	 "enter_yellow": func(_ context.Context, e *Event) {             
 	 	fmt.Println("enter_yellow")         
 	 },         
 	 "enter_state": func(_ context.Context, e *Event) {             
 	 	fmt.Println("enter_state")         
 	 },         
 	 "after_warn": func(_ context.Context, e *Event) {             
 	 	fmt.Println("after_warn")         
 	 },         
 	 "after_event": func(_ context.Context, e *Event) {             
 	 	fmt.Println("after_event")         
 	 },     
 	},
```  

Usage as a struct field

```go
package main

import (
    "context"
    "fmt"
    "github.com/looplab/fsm"
)

type Door struct {
    To  string
    FSM *fsm.FSM
}

func NewDoor(to string) *Door {
    d := &Door{
        To: to,
    }

    d.FSM = fsm.NewFSM(
        "closed",
        fsm.Events{
            {Name: "open", Src: []string{"closed"}, Dst: "open"},
            {Name: "close", Src: []string{"open"}, Dst: "closed"},
        },
        fsm.Callbacks{
            //切换状态后触发
            "enter_state": func(_ context.Context, e *fsm.Event) { d.enterState(e) },
        },
    )

    return d
}

func (d *Door) enterState(e *fsm.Event) {
    fmt.Printf("The door to %s is %s\n", d.To, e.Dst)
}

func main() {
    door := NewDoor("heaven")

    err := door.FSM.Event(context.Background(), "open")
    if err != nil {
        fmt.Println(err)
    }

    err = door.FSM.Event(context.Background(), "close")
    if err != nil {
        fmt.Println(err)
    }
}
```
结果：
The door to heaven is open
The door to heaven is closed  

Door就是拥有有限状态的事物，FSM封装了事件及事件对应的状态转移关系，要在事件触发时做业务逻辑处理，只需在callbacks中定义即可，可以根据事件也可以根据状态来定义(before,leave,after,enter等)  

door.FSM.Event(context.Background(), "open")触发open事件，state由closed转向open，切换完状态后触发callbacks,最终执行d.enterState(e)方法
FSM.Event(ctx context.Context, event string, **args ...interface{}**)可以使用最后一个参数向callback中传参(fsm.Event.Args获取)  

***注意区分Events和Event的区别***  
Events时EventDesc类型的切片
```go
type Events []EventDesc
type EventDesc struct {
    Name string
    Src  []string
    Dst  string
}

type Event struct {
    FSM        *FSM
    Event      string
    Src        string
    Dst        string
    Err        error
    Args       []interface{}
    canceled   bool
    async      bool
    cancelFunc func()
}
```

## 源码分析
```go
type FMS struct{
    ....
    // transitions maps events and source states to destination states.
    transitions map[eKey]string

    // callbacks maps events and targets to callback functions.
    callbacks map[cKey]Callback
    .....
}
// cKey is a struct key used for keeping the callbacks mapped to a target.
type cKey struct {
    // target is either the name of a state or an event depending on which
    // callback type the key refers to. It can also be "" for a non-targeted
    // callback like before_event.
    target string

    // callbackType is the situation when the callback will be run.
    callbackType int
}

// eKey is a struct key used for storing the transition map.
type eKey struct {
    // event is the name of the event that the keys refers to.
    event string

    // src is the source from where the event can transition.
    src string
}

```
transitions就是记录事件EventDesc的集合(拆分成eKey{event,src},string{dest})
callbacks就是记录回调函数的集合(cKey为key，key中含有事件名或状态名，回调函数类型--什么时候触发)

### 状态转移流程分析
1.  先生成eKey{event-name,current-state},使用transitions检验是否含有该事件，没有就返回异常InvalidEventError/UnknownEventError
2.  生成事件Event,
    1. beforeEventCallbacks(ctx context.Context, e \*Event),检查callbacks是否含有key为cKey{e.Event/"", **callbackBeforeEvent**}值，有的话就执行(before_event或before_\<EVENT\>定义的callback)
    2. 如果当前状态和目标状态一致(current-state==dst-state),检查callbacks是否含有cKey{e.Event/"",**callbackAfterEvent**}的值，有的话就执行(after_\<EVENT\>或after_event定义的callback),最后返回错误NoTransitionError
    3. 定义转换函数，函数中设置FSM的state，执行enterStateCallbacks和afterEventCallbacks
    4. leaveStateCallbacks(ctx context.Context, e \*Event)检查callbacks是否含有key为cKey{e.Event/"", **callbackLeaveState**}值,有的话就执行（leave_\<OLD_STATE\>/leave_state定义的callback）
    5. 执行转移函数，第3步定义的函数，进行状态转移


