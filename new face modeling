import { useEffect, useState } from "react";
import { getApp, getApps, initializeApp } from "firebase/app";
import {
  createUserWithEmailAndPassword,
  getAuth,
  onAuthStateChanged,
  signInWithEmailAndPassword,
  signOut,
  User,
} from "firebase/auth";
import {
  addDoc,
  collection,
  deleteDoc,
  doc,
  getDocs,
  getFirestore,
} from "firebase/firestore";

type Model = {
  id: string;
  name: string;
  category: string;
  createdAt?: string;
};

const firebaseConfig = {
  apiKey: "AIzaSyAFWuJubUq1Jkq3BSLXAuiU4iErvPa33mc",
  authDomain: "new-face-models.firebaseapp.com",
  projectId: "new-face-models",
  storageBucket: "new-face-models.firebasestorage.app",
  messagingSenderId: "1072170627358",
  appId: "1:1072170627358:web:a6183da9d1f99eb3a4d90f",
};

const firebaseApp = getApps().length
  ? getApp()
  : initializeApp(firebaseConfig);

const auth = getAuth(firebaseApp);
const db = getFirestore(firebaseApp);

const ADMIN_EMAIL = "thh09977@gmail.com";

export default function NewFaceModelingWebsite() {
  const [user, setUser] = useState<User | null>(null);
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");
  const [showModal, setShowModal] = useState(false);
  const [isLogin, setIsLogin] = useState(true);
  const [loading, setLoading] = useState(false);
  const [models, setModels] = useState<Model[]>([]);
  const [loadingModels, setLoadingModels] = useState(false);

  const isAdmin =
    user?.email?.toLowerCase() === ADMIN_EMAIL.toLowerCase();

  useEffect(() => {
    const unsubscribe = onAuthStateChanged(auth, (currentUser) => {
      setUser(currentUser);
    });

    return () => unsubscribe();
  }, []);

  useEffect(() => {
    if (isAdmin) {
      void fetchModels();
    }
  }, [isAdmin]);

  const fetchModels = async () => {
    setLoadingModels(true);

    try {
      const snapshot = await getDocs(collection(db, "models"));

      const data: Model[] = snapshot.docs.map((item) => {
        const value = item.data() as Omit<Model, "id">;

        return {
          id: item.id,
          name: value.name || "Unknown",
          category: value.category || "Unknown",
          createdAt: value.createdAt,
        };
      });

      setModels(data);
    } catch (error) {
      console.error("Failed to fetch models", error);
    } finally {
      setLoadingModels(false);
    }
  };

  const resetFields = () => {
    setEmail("");
    setPassword("");
  };

  const handleAuth = async (
    event: React.FormEvent<HTMLFormElement>
  ) => {
    event.preventDefault();

    if (!email.trim() || !password.trim()) {
      alert("Email and password are required");
      return;
    }

    setLoading(true);

    try {
      if (isLogin) {
        await signInWithEmailAndPassword(
          auth,
          email.trim(),
          password
        );
      } else {
        await createUserWithEmailAndPassword(
          auth,
          email.trim(),
          password
        );
      }

      resetFields();
      setShowModal(false);
    } catch (error) {
      const message =
        error instanceof Error
          ? error.message
          : "Authentication failed";

      alert(message);
    } finally {
      setLoading(false);
    }
  };

  const handleLogout = async () => {
    try {
      await signOut(auth);
      setModels([]);
    } catch (error) {
      console.error("Logout failed", error);
    }
  };

  const addModel = async () => {
    const name = window.prompt("Enter model name")?.trim();
    const category = window.prompt("Enter model category")?.trim();

    if (!name || !category) {
      return;
    }

    try {
      await addDoc(collection(db, "models"), {
        name,
        category,
        createdAt: new Date().toISOString(),
      });

      await fetchModels();
    } catch (error) {
      console.error("Failed to add model", error);
    }
  };

  const deleteModel = async (id: string) => {
    try {
      await deleteDoc(doc(db, "models", id));

      setModels((currentModels) => {
        return currentModels.filter((model) => model.id !== id);
      });
    } catch (error) {
      console.error("Failed to delete model", error);
    }
  };

  return (
    <div className="min-h-screen bg-black font-sans text-white">
      <div className="fixed right-5 top-5 z-50 flex gap-3">
        {!user ? (
          <button
            type="button"
            onClick={() => setShowModal(true)}
            className="rounded-xl bg-yellow-500 px-5 py-2 font-bold text-black transition hover:bg-yellow-400"
          >
            Models Login
          </button>
        ) : (
          <button
            type="button"
            onClick={handleLogout}
            className="rounded-xl bg-red-500 px-5 py-2 font-bold text-white transition hover:bg-red-400"
          >
            Logout
          </button>
        )}
      </div>

      {showModal && (
        <div className="fixed inset-0 z-50 flex items-center justify-center bg-black/80 px-4">
          <form
            onSubmit={handleAuth}
            className="w-full max-w-md rounded-3xl border border-yellow-500 bg-zinc-900 p-8"
          >
            <h2 className="mb-6 text-center text-3xl font-bold text-yellow-500">
              {isLogin ? "Model Login" : "Model Register"}
            </h2>

            <input
              type="email"
              placeholder="Email"
              value={email}
              onChange={(event) => setEmail(event.target.value)}
              className="mb-4 w-full rounded-xl border border-zinc-700 bg-black p-4 outline-none"
              autoComplete="email"
              required
            />

            <input
              type="password"
              placeholder="Password"
              value={password}
              onChange={(event) => setPassword(event.target.value)}
              className="mb-6 w-full rounded-xl border border-zinc-700 bg-black p-4 outline-none"
              autoComplete={isLogin ? "current-password" : "new-password"}
              minLength={6}
              required
            />

            <button
              type="submit"
              disabled={loading}
              className="w-full rounded-xl bg-yellow-500 py-4 font-bold text-black transition hover:bg-yellow-400 disabled:cursor-not-allowed disabled:opacity-70"
            >
              {loading
                ? "Please wait..."
                : isLogin
                ? "Login"
                : "Register"}
            </button>

            <button
              type="button"
              onClick={() => setIsLogin((current) => !current)}
              className="mt-5 w-full text-center text-zinc-400 transition hover:text-white"
            >
              {isLogin
                ? "No account? Register"
                : "Already have account? Login"}
            </button>

            <button
              type="button"
              onClick={() => {
                setShowModal(false);
                resetFields();
              }}
              className="mt-4 w-full text-zinc-500 transition hover:text-white"
            >
              Cancel
            </button>
          </form>
        </div>
      )}

      <section className="relative flex min-h-screen flex-col items-center justify-center overflow-hidden px-6 text-center">
        <div className="absolute inset-0 bg-gradient-to-b from-black via-zinc-900 to-black" />

        <nav className="absolute top-0 left-0 right-0 z-20 flex items-center justify-between px-8 py-6">
          <h2 className="text-2xl font-bold tracking-widest">
            <span className="text-white">NEW</span>{" "}
            <span className="text-orange-500">FACE</span>
          </h2>

          <div className="hidden gap-8 text-sm uppercase tracking-wider md:flex">
            <a href="#home" className="hover:text-yellow-400">Home</a>
            <a href="#models" className="hover:text-yellow-400">Models</a>
            <a href="#agency" className="hover:text-yellow-400">Agency</a>
            <a href="#contact" className="hover:text-yellow-400">Contact</a>
          </div>
        </nav>

        <div className="relative z-10 flex min-h-screen flex-col items-center justify-center">
        <h1 className="text-6xl font-extrabold tracking-wider md:text-8xl">
          <span className="text-white">NEW</span>{" "}
          <span className="text-orange-500">FACE</span>
        </h1>

        <p className="mt-4 text-xl uppercase tracking-[0.3em] text-yellow-500">
          MODEL MANAGEMENT SYSTEM
        </p>

        {user && (
          <div className="mt-10 w-full max-w-md rounded-2xl border border-yellow-500 bg-zinc-900 p-6">
            <h2 className="text-2xl font-bold text-yellow-500">
              Welcome
            </h2>

            <p className="mt-2 break-all text-zinc-300">
              {user.email}
            </p>

            <p className="mt-3 text-sm text-zinc-400">
              Role: {isAdmin ? "ADMIN" : "MODEL"}
            </p>
          </div>
        )}

        {isAdmin && (
          <div className="mt-12 w-full max-w-4xl rounded-3xl border border-red-500 bg-zinc-900 p-8">
            <div className="mb-6 flex items-center justify-between">
              <h2 className="text-3xl font-bold text-red-400">
                Admin Panel
              </h2>

              <button
                type="button"
                onClick={addModel}
                className="rounded-xl bg-red-500 px-5 py-3 font-bold text-white transition hover:bg-red-400"
              >
                Add Model
              </button>
            </div>

            {loadingModels ? (
              <p className="text-zinc-400">Loading models...</p>
            ) : models.length === 0 ? (
              <p className="text-zinc-500">No models found.</p>
            ) : (
              <div className="space-y-4">
                {models.map((model) => (
                  <div
                    key={model.id}
                    className="flex items-center justify-between rounded-2xl border border-zinc-700 bg-black p-4"
                  >
                    <div>
                      <h3 className="text-lg font-bold text-yellow-500">
                        {model.name}
                      </h3>

                      <p className="text-sm text-zinc-400">
                        {model.category}
                      </p>
                    </div>

                    <button
                      type="button"
                      onClick={() => deleteModel(model.id)}
                      className="font-bold text-red-400 transition hover:text-red-300"
                    >
                      Delete
                    </button>
                  </div>
                ))}
              </div>
            )}
          </div>
        )}
              </div>
      </section>

      <section
        id="models"
        className="bg-zinc-950 px-6 py-24"
      >
        <div className="mx-auto max-w-6xl">
          <div className="mb-14 text-center">
            <h2 className="text-5xl font-bold text-yellow-500">
              Featured Models
            </h2>
            <p className="mt-4 text-zinc-400">
              Discover elite talents from NEW FACE Modeling.
            </p>
          </div>

          <div className="grid gap-8 md:grid-cols-3">
            {[1, 2, 3].map((item) => (
              <div
                key={item}
                className="overflow-hidden rounded-3xl border border-zinc-800 bg-black transition hover:-translate-y-2 hover:border-yellow-500"
              >
                <div className="h-80 bg-gradient-to-b from-zinc-800 to-black" />

                <div className="p-6 text-left">
                  <h3 className="text-2xl font-bold text-yellow-500">
                    Model {item}
                  </h3>

                  <p className="mt-2 text-zinc-400">
                    Fashion • Runway • Commercial
                  </p>
                </div>
              </div>
            ))}
          </div>
        </div>
      </section>

      <section
        id="agency"
        className="bg-black px-6 py-24"
      >
        <div className="mx-auto grid max-w-6xl gap-12 md:grid-cols-2 md:items-center">
          <div>
            <h2 className="text-5xl font-bold text-orange-500">
              About Agency
            </h2>

            <p className="mt-6 text-lg leading-8 text-zinc-400">
              NEW FACE is a professional modeling management platform helping talents connect with brands, fashion companies, and international opportunities.
            </p>
          </div>

          <div className="grid gap-6 md:grid-cols-2">
            <div className="rounded-3xl border border-zinc-800 bg-zinc-900 p-8">
              <h3 className="text-3xl font-bold text-yellow-500">150+</h3>
              <p className="mt-2 text-zinc-400">Registered Models</p>
            </div>

            <div className="rounded-3xl border border-zinc-800 bg-zinc-900 p-8">
              <h3 className="text-3xl font-bold text-yellow-500">25+</h3>
              <p className="mt-2 text-zinc-400">Brand Partners</p>
            </div>
          </div>
        </div>
      </section>

      <section
        id="contact"
        className="bg-zinc-950 px-6 py-24"
      >
        <div className="mx-auto max-w-4xl rounded-3xl border border-yellow-500 bg-black p-10 text-center">
          <h2 className="text-5xl font-bold text-yellow-500">
            Contact Agency
          </h2>

          <p className="mt-6 text-zinc-400">
            MODEL AND COACH YOHANIS WERKNE
          </p>

          <div className="mt-10 flex flex-wrap items-center justify-center gap-6">
            <button className="rounded-2xl bg-yellow-500 px-8 py-4 font-bold text-black transition hover:bg-yellow-400">
              Become a Model
            </button>

            <button className="rounded-2xl border border-yellow-500 px-8 py-4 font-bold text-yellow-500 transition hover:bg-yellow-500 hover:text-black">
              Book Agency
            </button>
          </div>
        </div>
      </section>
    </div>
  );
}
