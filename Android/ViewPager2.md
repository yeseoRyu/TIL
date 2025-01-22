ViewPager2
===

* ViewPager2는 페이지 간에 전환하는 스와이프 기능을 제공하는 라이브러리

<br>

### Fragment를 페이지로 사용

FragmentStateAdapter를 생성하여 ViewPager2 객체에 연결하여 Fragment를 페이지로 사용한다.   

FragmentStateAdapter가 createFragment()로 Fragment의 인스턴스를 새 페이지로 제공하고,   
getItemCount() 메서드로 어댑터가 만든 페이지 수를 호출한다.   

<br>

### TabLayoutMediator
* TabLayout을 ViewPager2에 연결하는 매개체.   

탭이 선택되면 ViewPager2의 위치를 ​​선택한 탭과 동기화하고,   
사용자가 ViewPager2를 끌면 TabLayout의 스크롤 위치를 동기화한다.

* IllegalStateException
  - TabLayoutMediator가 이미 연결되어 있거나 ViewPager2에 어댑터가 없는 경우 예외를 던진다.


            TabLayoutMediator(tabLayout, viewPager) { tab, position ->
            tab.icon = ContextCompat.getDrawable(this, tabIcons[position])
            tab.text = when (position) {
                0 -> getString(R.string.home)
                1 -> getString(R.string.bookmark)
                else -> throw IllegalStateException("Unexpected position $position")
            }
        }.attach()