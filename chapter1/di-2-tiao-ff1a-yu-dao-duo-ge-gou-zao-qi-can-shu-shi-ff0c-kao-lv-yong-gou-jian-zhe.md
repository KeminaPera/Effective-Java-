Static factories and constructors share a limitation: they do not scale well to large numbers of optional parameters. Consider the case of a class representing the Nutrition Facts label that appears on packaged foods. These labels have a few required fields—serving size, servings per container, and calories per serving—and more than twenty optional fields—total fat, saturated fat, trans fat, cholesterol, sodium, and so on. Most products have nonzero values for only a few of these optional fields.

What sort of constructors or static factories should you write for such a class? Traditionally, programmers have used the telescoping constructor pattern, in which you provide a constructor with only the required parameters, another with a single optional parameter, a third with two optional parameters, and so on, culminating in a constructor with all the optional parameters. Here’s how it looks in practice. For brevity’s sake, only four optional fields are shown:

静态工厂和构造器都有个共同的不足的地方：它们都不能很好地扩展到大量的可选参数。考虑这么一种情况，用一个类来表示包装食品外面的营养成分标签。这些标签里有几个域是必须有的：每份的含量，每罐的含量以及每份的卡路里。还有

