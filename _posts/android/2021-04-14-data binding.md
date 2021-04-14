---
published: true
title: "[Android] DatabindingImpl 소스 분석"	
excerpt : " "	
layout: single	
author_profile: true	
read_time: false # read_time을 출력할지 여부 1min read 같은것!	
toc: true #Table Of Contents 목차 보여줌	
toc_label: "My Table of Contents" # toc 이름 정의	
toc_icon: "cog" #font Awesome아이콘으로 toc 아이콘 설정	
toc_sticky: true # 스크롤 내릴때 같이 내려가는 목차	
categories :	
 - Android	
tags: 	
  - Android	
comments: true	
header:	
  overlay_filter: 0.5	
sitemap :	
  changefreq : daily	
  priority : 1.0	
  priority : 1.0---
---

Android Databing을 할 시 자동으로 생성되는 DatabindingImpl 소스를 분석하여 내부적으로 Databinding이 어떻게 동작하는 지 알아보는 포스트입니다.

## Databinding Library

데이터 바인딩 라이브러리는 프로그래밍 방식이 아닌 선언적 형식을 사용하여 레이아웃의 UI 구성 요소를 앱의 데이터 소스에 바인딩 할 수있는 지원 라이브러리입니다.

## Sample Code

분석할 때 사용한 코드는 아래 Tic Tac Toe github repo. 를 참고했습니다.

<div align="center">

<img src="https://user-images.githubusercontent.com/35194820/114659874-4fa26a80-9d2f-11eb-85ad-9a2e4d90954d.gif" >

<a href="https://github.com/ericmaxwell2003/ticTacToe">github</a>

</div>

### Tic Tac Toe 

틱택토 게임이란 두 사람이 번갈아 가면서 'O' 또는 'X' 를 그리며 가장 먼저 3개를 직선으로 만들면 이기는 게임입니다.

<div align="center">

<img src="https://user-images.githubusercontent.com/35194820/114660281-fd157e00-9d2f-11eb-8d6f-a23a8fa41276.gif" >

</div>


### Sample Code Structure

위 샘플코드는 MVVM 디자인 패턴을 따르며 구조는 아래와 같습니다.

![스크린샷 2021-04-14 오후 1 15 40](https://user-images.githubusercontent.com/35194820/114653827-db15fe80-9d23-11eb-9253-101a46318c4d.png)

### View Model Code

~~~java
public class TicTacToeViewModel implements ViewModel {

    private Board model;

    /* 
     * These are observable variables that the viewModel will update as appropriate
     * The view components are bound directly to these objects and react to changes
     * immediately, without the ViewModel needing to tell it to do so. They don't
     * have to be public, they could be private with a public getter method too.
     */
    public final ObservableArrayMap<String, String> cells = new ObservableArrayMap<>();
    public final ObservableField<String> winner = new ObservableField<>();

    public TicTacToeViewModel() {
        model = new Board();
    }

    // As with presenter, we implement standard lifecycle methods from the view
    // in case we need to do anything with our model during those events.
    public void onCreate() { }
    public void onPause() { }
    public void onResume() { }
    public void onDestroy() { }

    /**
     * An Action, callable by the view.  This action will pass a message to the model
     * for the cell clicked and then update the observable fields with the current
     * model state.
     */
    public void onClickedCellAt(int row, int col) {
        Player playerThatMoved = model.mark(row, col);
        cells.put("" + row + col, playerThatMoved == null ? 
                                                     null : playerThatMoved.toString());
        winner.set(model.getWinner() == null ? null : model.getWinner().toString());
    }

    /**
     * An Action, callable by the view.  This action will pass a message to the model
     * to restart and then clear the observable data in this ViewModel.
     */
    public void onResetSelected() {
        model.restart();
        winner.set(null);
        cells.clear();
    }

}
~~~

위 코드에서 가장 중요하게 볼 것은 두 개의 observable 멤버 변수입니다.

observable 멤버 변수 값이 업데이트 되면 View 에 notify가 되어 View가 스스로 (개발자 입장에서) 업데이트 됩니다.

### View Code

~~~xml
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <!-- We will reference the TicTacToeViewModel by the name viewModel as we have defined it here. -->
    <data>
        <import type="android.view.View" />
        <variable name="viewModel" type="com.acme.tictactoe.viewmodel.TicTacToeViewModel" />
    </data>
    <LinearLayout...>
        <GridLayout...>
            <!-- onClick of any cell in the board, the button clicked will invoke the onClickedCellAt method with its row,col -->
            <!-- The display value comes from the ObservableArrayMap defined in the ViewModel  -->
            <Button
                style="@style/tictactoebutton"
                android:onClick="@{() -> viewModel.onClickedCellAt(0,0)}"
                android:text='@{viewModel.cells["00"]}' />
            ...
            <Button
                style="@style/tictactoebutton"
                android:onClick="@{() -> viewModel.onClickedCellAt(2,2)}"
                android:text='@{viewModel.cells["22"]}' />
        </GridLayout>

        <!-- The visibility of the winner view group is based on whether or not the winner value is null.
             Caution should be used not to add presentation logic into the view.  However, for this case
             it makes sense to just set visibility accordingly.  It would be odd for the view to render
             this section if the value for winner were empty.  -->
        <LinearLayout...
            android:visibility="@{viewModel.winner != null ? View.VISIBLE : View.GONE}"
            tools:visibility="visible">

            <!-- The value of the winner label is bound to the viewModel.winner and reacts if that value changes -->
            <TextView
                ...
                android:text="@{viewModel.winner}"
                tools:text="X" />
            ...
        </LinearLayout>
    </LinearLayout>
</layout>

~~~

View Code 를 살펴보면 9개의 Button 과 1개의 TextView 로 구성이 되어 있습니다.  
  
Button 같은 경우 ViewModel의 cells 와 바인딩이 되어 있고 Button을 눌었을 때 발생되는 event 즉 onClick 은 View Model의 onClickedCellAt 에 바인딩이 되어 있습니다.  
즉 다시 말해 Button에 보여지는 내용은 ViewModel의 cells 값을 사용하며 ViewModel의 cells 값이 변경되면 Button의 내용도 자동으로 변경이 됩니다.  
그리고 Button을 사용자가 터치하게 되면 ViewModel의 onClickedCellAt 메소드가 호출이 됩니다.

TextView 같은 경우 ViewModel의 winner와 바인딩이 되어 있고 winner 값이 변경이 되면 TextView 도 자동으로 변경이 됩니다.  

## TictactoeBindingImpl

위 코드를 빌드하게 되면 'TictactoeBindingImpl' 이 자동으로 생성이 됩니다.

![스크린샷 2021-04-14 오후 1 20 20](https://user-images.githubusercontent.com/35194820/114654023-3ea02c00-9d24-11eb-8e3a-0c0497c469af.png)

'TictactoeBindingImpl' 이 실질적으로 View 와 View Model을 서로 이어주는 역할 즉 View Model의 데이터를 읽어서 View 를 업데이트를 하거나 View에서 발생하는 이벤트를 View Model 에게 전달하는 역할을 수행합니다.

### Member valuable

'TictactoeBindingImpl' 은 아래와 같이 Button, TextView, onClick Listener, View Model을 가지고 있습니다.

~~~java
    // views
    @NonNull
    private final android.widget.Button mboundView1;
    @NonNull
    private final android.widget.Button mboundView2
    ...
    // variables
    @Nullable
    private final android.view.View.OnClickListener mCallback1;
    @Nullable
    private final android.view.View.OnClickListener mCallback2;

    com.acme.tictactoe.viewmodel.TicTacToeViewModel mViewModel;
~~~

### Touch Event 전달 과정

다음은 Button의 touch 가 발생할 경우 ViewModel의 onClickedCellAt() 메소드가 호출되는 지 살펴보겠습니다.
  
TictactoeBindingImpl 은 onClickListener interface를 구현하고 있습니다.

~~~java
public class TictactoeBindingImpl extends TictactoeBinding implements com.acme.tictactoe.generated.callback.OnClickListener.Listener {
 ...
}
~~~

사용자가 Button을 Touch 할 경우 _internalCallbackOnClick() callback이 호출이 되며 sourceID의 값에 따라 viewModel.onClickedCellAt() 의 인자값을 달리해서 호출하게 됩니다.
  
~~~java
public final void _internalCallbackOnClick(int sourceId , android.view.View callbackArg_0) {
  switch(sourceId) {
      case 2: {
          // localize variables for thread safety
          // viewModel
          com.acme.tictactoe.viewmodel.TicTacToeViewModel viewModel = mViewModel;
          // viewModel != null
          boolean viewModelJavaLangObjectNull = false;

          viewModelJavaLangObjectNull = (viewModel) != (null);
          if (viewModelJavaLangObjectNull) {
              viewModel.onClickedCellAt(0, 1);
          }
          break;
        
      }
      ...
~~~

여기서 sourceID는 Button의 고유 ID 입니다.

### View 업데이트 과정

다음은 View Model의 Observable Data 가 변경이 될 경우 View 가 어떻게 업데이트 되는 지 알아보겠습니다.

View Model의 observable Data 가 변경이 되면 `TictactoeBindingImpl` 의 부모 클래스인 ViewDataBinding 의 `handleFieldChange()` 메소드가 호출이 됩니다.

~~~java
private void handleFieldChange(int mLocalFieldId, Object object, int fieldId) {
    boolean result = onFieldChange(mLocalFieldId, object, fieldId);
    if (result) {
        requestRebind();
    }
}
~~~

위에 `onFieldChange()` 메소드를 통해서 어떤 Observable Data가 변경이 되었는지 확인하고 변경된 것이 있으면 `requestRebind()` 를 호출하고 `requestRebind()` 에서는 `executePendingBindings()` 를 다시 호출합니다. 그리고 마지막으로 `TictactoeBindingImpl` 의 `excuteBindings()` 를 호출합니다.

위 순서를 정리하자면

1. View Model의 Observable Data 변경
2. ViewDataBinding.handleFieldChange() 호출
3. ViewDataBinding.requestRebind() 호출
4. ViewDataBinding.executePendingBindings() 호출
5. TictactoeBindingImpl.excuteBindings() 호출
  
여기서 TictactoeBindingImpl.excuteBindings() 이 실질적으로 View Model의 Data를 읽어서 View 를 업데이트를 수행합니다.

~~~java
@Override
protected void executeBindings() {
  java.lang.String viewModelCells01 = null;
  java.lang.String viewModelCells11 = null;
  java.lang.String viewModelCells22 = null;
  
  ....
  
  android.databinding.ObservableArrayMap<java.lang.String,java.lang.String> viewModelCells = null;
  com.acme.tictactoe.viewmodel.TicTacToeViewModel viewModel = mViewModel;
  
  ...

  if (viewModel != null) {
      // read viewModel.cells
      viewModelCells = viewModel.cells;
  }
  ...
  if (viewModelCells != null) {
    // read viewModel.cells["01"]
    viewModelCells01 = viewModelCells.get("01");
    // read viewModel.cells["11"]
    viewModelCells11 = viewModelCells.get("11");
    // read viewModel.cells["22"]
    viewModelCells22 = viewModelCells.get("22");
    ...
  }
  ...
  // Set Text
  android.databinding.adapters.TextViewBindingAdapter.setText(this.mboundView1, viewModelCells00);
  android.databinding.adapters.TextViewBindingAdapter.setText(this.mboundView2, viewModelCells01);
  android.databinding.adapters.TextViewBindingAdapter.setText(this.mboundView3, viewModelCells02);
  ...
}
~~~

위 과정을 통해 ViewModel의 cells 의 데이터가 변경되었을 때 Button의 Text가 업데이트가 됩니다.