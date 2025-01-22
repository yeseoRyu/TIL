ListAdater
===

ListAdater는 RecyclerView.AdapterRecyclerView백그라운드 스레드에서   
목록 간의 차이점을 계산하는 것을 포함하여 목록 데이터를 표현하기 위한 기본 클래스이다.   

    public abstract class ListAdapter<T, VH extends RecyclerView.ViewHolder> extends RecyclerView.Adapter


 Recyclerview 어댑터를 ListAdapter로 구현하면 submitList로 전체 리스트를 넘겨주기만 하면    
 어댑터가 알아서 백그라운드 스레드를 사용해 리스트 차이를 계산하여 화면을 갱신시켜준다.   


## DiffUtil

DiffUtil은 두 데이터셋을 받아서 그 차이를 계산해주는 클래스이다.

DiffUtil.Callback()을 상속받아 아래의 메서드를 수현한다.

* areItemsTheSame
* areContentsTheSame 

areItemsTheSame 는 리스트에서 항목의 고유성(id)을 판별하고,   
areContentsTheSame 는 항목의 내용이 같은지 판별하여 UI가 업데이트 여부를 결정한다.   

새로운 데이터가 들어오면 기존 데이터와 비교하여   
달라진 부분만 UI를 업데이트하기 위해 사용되는 것이다.   


