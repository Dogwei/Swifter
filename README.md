# Swifter.Json

��������Ϊֹ .Net ƽ̨������ǿ��������ѵ� JSON ���л��ͷ����л��⡣

#### ֮����˵ǿ����Ϊ��Щ�����������û�У�
	1): ֧����ȸ��ӵĶ���ṹ������ʹ�á�
	2): �� $ref ��ʾ�ظ���ѭ�����õ����л��ͷ����л���
	3): ĿǰΨһ֧�� ref ���Ե� JSON �⡣ 
	4): ֧�ּ������������õ����ͣ����������Զ������͵���Ϊ��
	5): ֧�� .Net Framework 2.0 +, .Net Core 2.0+, .Net Standard 2.0+, Mono, Xamarin, Unity��

#### ����Ϊ�����죿
	1): ����������ͺ͸����� ToString �� Parse ����ʵ�֡�
	2): Emit ʵ�ֵĸ����ܶ���ӳ�乤�ߡ�
	3): �����ڴ���䣡�ܾ� .Net �йܶ����ڴ档
	4): ʹ���̻߳��棬�����ĳ�������Խ���ٶ�Խ�졣
	5): �ڲ�ȫָ�����㣬�൱��ʹ���� .Net Core �¼��� Span<T>��

#### Swifter.Json ʵ�ù���
	1): �������� Json��
	2): ������� 0 �� null �� "" ֵ��
	3): ����ʹ�� [RWField] ���Զ������Ի��ֶε���Ϊ��
	4): �����������������������ݴ�С��

#### Swifter.Json ֧�ֵ�����
	bool, byte, sbyte, char, shoft, ushoft, int, uint, long, ulong,
	float, double, decimal, string, enum DateTime, DateTimeOffset,
	Guid, TimeSpan, DBNull, Nullable<T>, Version, Type,
	Array, Multidimensional-Arrays, IList, IList<T>, ICollection,
	ICollection<T>, IDictionary, IDictionary<TKey, TValue>,
	IEnumerable, IEnumerable<T>, DataTable, DbDataReader ...
	�������ͽ��ᱻ���� Object���� ���Լ�/����ֵ ����ʽӳ�䡣

#### Swifter.Json ��ȫ��
	ÿ�η���֮ǰ�Ҷ����������һ���£����Ҿ��������Ĳ��ԣ���ʵ����Ŀ��ʹ��δ�����İ汾
	��ȷ�������汾���ȶ��ԡ�����ʹ��������Ҳ�޷���֤��һ����ȫ�����ԣ������������
	Bug ��ĳЩ������ĵط��뼰ʱ��ϵ�� QQ:1287905882������ 1287905882@qq.com��
	
#### ��ΰ�װ��

	```
	Nuget> Install-Package Swifter.Json -Version 1.1.2
	```

#### ���ܲ��ԶԱ�

	*:  ͼ���е���ɫ������ʱ��� ��ɫ ����Ϊ ��ɫ������ʱ���� 3 ��ʱ��������ɫ��ʾ��
			Timeout: ��ʾ��ʱ���á�
			Exception: ��ʾ�������쳣��
			Error: δ�����쳣�����������ȷ��

	*:	Swifter.Json ��һ��ִ����Ҫ�����ʱ��������һ�� �������ࡱ (FastObjectRW<T>)��
		֮��ִ�����Խ��Խ�졣����������ĳ�����Ҫ�������У���ô Swifter.Json �����ŵ�ѡ��
		��Ȼ������ĳ�����������ģʽ����ô������ܵ� XObjectRW<T> Ҳ���ʺ�����

	*:	����ʱ��������ʹ�õİ汾



#### ����ʾ��

	1): ��ʹ��

	```C#
    public class Demo
    {
        public int Id { get; set; }

        public string Name { get; set; }
        
        public static void Main()
        {
            var json = JsonFormatter.SerializeObject(new { Id = 1, Name = "Dogwei" });
            var dictionary = JsonFormatter.DeserializeObject<Dictionary<string, object>>(json);
            var obj = JsonFormatter.DeserializeObject<Demo>(json);
        }
    }
	```

	2): �����ظ�����

	```C#
    public class Demo
    {
        public int Id { get; set; }

        public string Name { get; set; }

        public Demo Instance { get; set; }

        public static void Main()
        {
            var jsonFormatter = new JsonFormatter(JsonFormatterOptions.MultiReferencingReference);

            var obj = new Demo() { Id = 1, Name = "Dogwei" };

            obj.Instance = obj;

            var json = jsonFormatter.Serialize(obj);

            var deser = jsonFormatter.Deserialize<Demo>(json);

            Console.WriteLine(json); // {"Id":1,"Instance":{"$ref":"#"},"Name":"Dogwei"}

            Console.WriteLine(deser.Instance == deser); // True
        }
    }
	```

	3): RWField ����

	```C#
    public class Demo
    {
        [RWField("First Name")]
        public string Name { get; set; }

        [RWField]
        public int Age;

        [RWField(Access = RWFieldAccess.Ignore)]
        public int Sex { get; set; }
        [RWField(Order = 1)]
        public int Id { get; set; }

        public static void Main()
        {
            var obj = new Demo() { Id = 1, Name = "Dogwei", Age = 22, Sex = 1 };

            var json = JsonFormatter.SerializeObject(obj);

            Console.WriteLine(json); // {"Id":1,"Age":22,"First Name":"Dogwei"}
        }
    }
	```

	4): �������ڸ�ʽ

	```C#
    public class Demo
    {
        public static void Main()
        {
            var jsonFormatter = new JsonFormatter();

            jsonFormatter.SetDateTimeFormat("yyyy-MM-dd HH:mm:ss");

            var json = jsonFormatter.Serialize(DateTime.Now);

            Console.WriteLine(json); // "2019-02-13 11:03:46"
        }
    }
	```

	5): �Զ������͵���Ϊ

	```C#
    public class Demo
    {
        public string Name { get; set; }

        public int Sex { get; set; }

        public bool IsMan { get => Sex == 1; }

        public unsafe static void Main()
        {
            var jsonFormatter = new JsonFormatter();
            
            jsonFormatter.SetValueInterface<bool>(new MyBooleanInterface());

            var obj = new Demo() { Name = "Dogwei", Sex = 1 };

            var json = jsonFormatter.Serialize(obj);

            Console.WriteLine(json); // {"IsMan":"yes","Name":"Dogwei","Sex":1}
        }
    }

    public class MyBooleanInterface : IValueInterface<bool>
    {
        public bool ReadValue(IValueReader valueReader)
        {
            var value = valueReader.ReadString();

            switch (value)
            {
                case "yes":
                case "true":
                    return true;
                case "no":
                case "false":
                    return false;
                default:
                    return Convert.ToBoolean(value);
            }
        }

        public void WriteValue(IValueWriter valueWriter, bool value)
        {
            valueWriter.WriteString(value ? "yes" : "no");
        }
    }
	```

	6): ���û����С

	```C#
    public class Demo
    {
        public static void Main()
        {
            HGlobalCache.MaxSize = 1024 * 500; // 500KB

            var json = JsonFormatter.SerializeObject(new { MaxJsonLength = 256000 });
        }
    }
	```

	7): ���л������ļ�

	```C#
    public class Demo
    {
        public static void Main()
        {
            var bigObject = new BigObject();

            using (FileStream fileStream = new FileStream("/BigObject.json", FileMode.Create, FileAccess.ReadWrite))
            {
                using (StreamWriter streamWriter = new StreamWriter(fileStream))
                {
                    JsonFormatter.SerializeObject(bigObject, streamWriter);
                }
            }
        }
    }
	```

	8): ʹ������С��Ӧ�ó���� XObjectRW<T>

	```C#
    public class Demo
    {
        public static void Main()
        {
            // Default (FastObjectInterface)    : ��ʼ�������ϴ��ڴ�ϴ��������졣
            // XObjectInterface                 : ��ʼ������С���ڴ�ռ���٣�����Ҳ����

            ValueInterface.DefaultObjectInterfaceType = typeof(XObjectInterface<>);

            var json = JsonFormatter.SerializeObject(new { Id = 1, Name = "Dogwei" });

            Console.WriteLine(json); // {"Id":1,"Name":"Dogwei"}
        }
    }
	```


# Swifter.Core

Swifter.Core ������������������ƣ���д��������� .Net ����

#### Swifter.Core ʵ�ֵ��콢���ܣ�
	1): �������г������͵ĳ������ܶ���ӳ�乤�ߡ�
	2): Ч�ʳ�����ѧ�㷨���� .Net �Դ��㷨 10+ ����
	3): ���ŵ�ί�нӿڣ���������ʵ�ã�������õ�ί�аɡ�
	4): �������ܵĻ��湤�ߡ��̰߳�ȫ�� ��/�� ��ȡ���ܣ����̲߳���ȫ�� Dictionary ��Ҫ��������
	5): ����ָ�빤�ߣ���������ȡ����ĵ�ַ���ֶ�ƫ���������͵Ĵ�С�ȵײ���Ϣ��
	6): �����ܵ�����ת������ XConvert������������������ת��Ϊ�������ͣ�ֻҪ���Ǳ���֧��ת����
	7): ��� .Net20 �� .Net471 �İ汾�������⡣ ���� Swifter.Core �������ڵͰ汾��ʹ�� Ԫ�飬dynamic��LINQ �ȡ�


#### ��֧��ӳ��Ķ����ֵ������
	bool, byte, sbyte, char, shoft, ushoft, int, uint, long, ulong,
	float, double, decimal, string, enum DateTime, DateTimeOffset,
	Guid, TimeSpan, DBNull, Nullable<T>, Version, Type,
	Array, Multidimensional-Arrays, IList, IList<T>, ICollection,
	ICollection<T>, IDictionary, IDictionary<TKey, TValue>,
	IEnumerable, IEnumerable<T>, DataTable, DbDataReader ...
	�������ͽ��ᱻ���� Object���� ���Լ�/����ֵ ����ʽӳ�䡣

#### ��Ч����ѧ�㷨
	1): �����ּӼ��˳��㷨
	2): ���ͺ͸����� 2-64 ���� ToString �� Parse �㷨
	3): Guid �� ʱ��� ToString �� Parse �㷨��

#### ���¼���
	1): Difference-Switch �ַ���ƥ���㷨���� Hash ƥ��� 3 ����
	2): ֧�� ref ���ԣ�������������ʵ���ж��� ref ���Խ��ͳ����ڴ�����
	3): �ڲ�����ʵ�ִ���ί�У�֧�ִ��� 99.9% ������ί�У������� TypedReference* ��Ϊ�������͵ķ�����֧�֡���

#### ��ѧ�㷨���ܶԱ�

#### ����ӳ������ܶԱ�

#### �������ܶԱ�

#### ί�ж�ִ̬�е����ܶԱ�


#### �߶��淨
	1): ��һ������ĸ��Ƶ��ֵ��У�

	��֮��Ȼ��


	2): ��һ������תΪ�ṹ��ַ������������˽���ֶε�ֵ��

	3): ��һ������ת��Ϊ 52 ���Ƶ��ַ�����