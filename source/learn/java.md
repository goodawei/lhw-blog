    public static final Map<String, Api> apiMap = new HashMap<String, Api>();


    Set<Map.Entry<String, Api>> set = ApiRoute.apiMap.entrySet();



    List<String> params = new ArrayList<>();


--------  泛型 -------

普通声明：
List arrayList = new ArrayList();
arrayList.add("aaaa");
arrayList.add(100);
使用的时候要做类型判断，以及强制转型之类的：   String item = (String)arrayList.get(i);

泛型声明：

List<String> arrayList = new ArrayList<String>();
...
//arrayList.add(100); 在编译阶段，编译器就会报错



上界
public void processElements(List<? extends A> elements){
   for(A a : elements){
      System.out.println(a.getValue());
   }
}

下界
public static void insertElements(List<? super A> list){
    list.add(new A());
    list.add(new B());
    list.add(new C());
}


